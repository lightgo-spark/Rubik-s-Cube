# Faster R-CNN GUI Pro

A two-stage object detection stack written from scratch: PyTorch models and a
FastAPI inference server, driven by a native Rust + egui desktop client.

Detection architectures (Faster R-CNN, Cascade R-CNN, FCOS), the training loop,
the losses, the anchor generation, the NMS variants and the COCO evaluator are
implemented in this repository rather than imported from a detection framework.
`torchvision` is used as a library — for backbones, `roi_align`, `batched_nms`
and pretrained weights — not as the detector.

**Measured, not claimed:** Faster R-CNN R50-FPN reaches **COCO mAP 34.23**
(AP50 57.86, AP75 36.09) on `val2017` with imported torchvision weights — the
run is recorded in `results_tv_frcnn_r50.json`. 1,186 Rust tests pass, and 218
Python tests pass with 1 skipped (measured 2026-07-22); `cargo clippy
--all-targets` and `ruff` are clean under the CI invocation in
`.github/workflows/ci.yml`, which documents the style rules it excludes.

---

## Contents

- [Architecture](#architecture)
- [Quick start](#quick-start)
- [Training](#training)
- [Serving](#serving)
- [The desktop GUI](#the-desktop-gui)
- [Export](#export)
- [Testing](#testing)
- [Licensing and legal](#licensing-and-legal)
- [Project layout](#project-layout)

---

## Architecture

```
                 ┌──────────────────────────────────────────────┐
                 │  faster_rcnn_gui  (Rust + egui, 20 tabs)     │
                 │  local ONNX inference via ort + DirectML     │
                 └───────────────┬──────────────────────────────┘
                                 │ HTTP (optional API key)
                 ┌───────────────▼──────────────────────────────┐
                 │  server.py  (FastAPI, 30+ routes)            │
                 │  auth · rate limiting · caching · metrics    │
                 └───────────────┬──────────────────────────────┘
                                 │
     ┌───────────────────────────┼───────────────────────────┐
     │                           │                           │
┌────▼─────┐              ┌──────▼──────┐            ┌───────▼────────┐
│ model.py │              │ train.py    │            │ export_model.py│
│ Faster   │              │ DDP · AMP   │            │ ONNX ·         │
│ Cascade  │              │ EMA · sched │            │ TorchScript    │
│ FCOS     │              │ registry    │            │ scriptable     │
└──────────┘              └─────────────┘            └────────────────┘
```

The three detector families share one anchor/assignment/loss vocabulary, so a
change to IoU-family losses or NMS applies to all of them.

### What is implemented here

| Area | Detail |
|---|---|
| Detectors | Faster R-CNN, Cascade R-CNN (3 stages, IoU 0.5/0.6/0.7), FCOS (one-stage, anchor-free) |
| Backbones | ResNet-50, ResNet-101, MobileNetV3-Large, each with FPN P2–P6 |
| Losses | Smooth L1, GIoU, DIoU, CIoU, focal loss, label smoothing |
| Post-processing | NMS, Soft-NMS (vectorised), Weighted Box Fusion, per-class thresholds |
| Training | DDP, AMP, EMA, gradient accumulation, multi-scale, early stopping, cosine/multistep schedules |
| Augmentation | Mosaic, MixUp, CutOut, scale/crop jitter |
| Evaluation | COCO mAP with per-class AP, VOC mAP, calibration (temperature and Platt scaling, ECE) |
| Serving | FastAPI with API-key auth, rate limiting, SHA-256 keyed inference cache (memory or Redis), Prometheus metrics, async jobs |
| Annotation | 5 shapes — box, oriented box, polygon, keypoint (with per-point visibility), mask — exported to 7 formats |
| Export | ONNX (scriptable graph), TorchScript, COCO, VOC, YOLO detect/seg/pose/OBB, DOTA |

---

## Quick start

Requires Python 3.10 or 3.11, and a Rust toolchain for the GUI.

```bash
pip install -r requirements.txt

# Fetch torchvision's COCO-pretrained weights and convert them to this
# project's layout. Read NOTICE section 3 first — these weights carry dataset
# conditions that this project's own license does not cover.
python load_torchvision_weights.py

python server.py                      # http://localhost:8000
```

```bash
cd faster_rcnn_gui
cargo run --release
```

Without a checkpoint the server still starts and reports its state on `/health`;
set `PRODUCTION=1` to make it refuse to serve an untrained model instead.

---

## Training

```bash
python train.py --config config.yaml
```

`config.yaml` is the full surface: model and backbone, dataset root and format
(COCO or VOC), schedule, loss choice, AMP/EMA, export and registry behaviour.
Every key is validated at load time and unknown keys are reported rather than
ignored.

Command-line flags override the config file:

```bash
python train.py --data_root ./data/coco --dataset coco \
                --model cascade --backbone resnet101 \
                --box_loss ciou --amp --ema --epochs 26
```

Multi-GPU is `torchrun`, not a flag:

```bash
torchrun --nproc_per_node=4 train.py --config config.yaml
```

### Model registry

`--register_best` copies the best checkpoint into a versioned registry with its
metrics, its class list, and — importantly — its provenance:

```bash
python model_registry.py register checkpoints/best.pth --name frcnn-r50 \
  --source "trained on internal dataset from an ImageNet-pretrained backbone" \
  --license "internal use only; ImageNet backbone terms apply" \
  --training_data "./data/internal-2026-07"
```

The provenance fields exist because a `.pth` file records nothing about where it
came from. Once a checkpoint is in production, "what was this trained on, and
may we ship it?" has to be answerable — and it only is if someone recorded the
answer at registration time. `train.py` fills these in automatically.

---

## Serving

```bash
export API_KEY=...            # gates every endpoint
export PRODUCTION=1           # refuse untrained models, wildcard CORS, and no API_KEY
python server.py
```

Or with Docker:

```bash
docker compose up --build
```

Selected routes:

| Route | Purpose |
|---|---|
| `POST /detect` | single image |
| `POST /detect_batch` | up to `BATCH_MAX_FILES` images |
| `POST /detect_async` → `GET /jobs/{id}` | queued inference with backpressure |
| `POST /reload`, `/load_checkpoint` | hot-swap weights (path-whitelisted) |
| `GET /registry/models`, `POST /registry/load` | registry browse and deploy |
| `POST /export_coco`, `/export_yolo`, … | annotation format conversion |
| `GET /health`, `/metrics` | readiness and Prometheus metrics |

`PRODUCTION=1` refuses to start when `API_KEY` is unset. That is deliberate: an
unauthenticated `/detect` is both a denial-of-service vector and, once webcam
frames reach it, an exposure of personal data.

All configuration is in `.env.example`.

---

## The desktop GUI

`faster_rcnn_gui` is a native client, not a web wrapper. It runs detection two
ways: through the Python server, or locally against an ONNX model via
`ort` with the DirectML execution provider.

Twenty tabs cover detection, live webcam, video with an IoU tracker, annotation,
batch processing, a pipeline builder, benchmarking, charts, calibration, model
download and validation, and report generation.

**Model download.** The GUI searches Hugging Face and shows each result's
license as a coloured badge — permissive, copyleft, restricted, or *no license
declared*. A repository that declares no license is under default copyright and
may not be redistributed, so it is flagged rather than shown as neutral. When a
model is downloaded, a `<filename>.license.json` sidecar is written beside the
weights recording the source URL and the declared license.

**Webcam.** Frames are captured through `nokhwa` and written to a temporary file
for inference. They are not transmitted anywhere except to the inference backend
you configured. See [Privacy](#privacy) below.

---

## Export

```bash
python export_model.py --checkpoint checkpoints/best.pth --format onnx
```

ONNX export goes through a scriptable graph (`scriptable_export.py`) rather than
tracing, so control flow survives. Export parity is contract-tested: the gate
compares detection counts, boxes, labels and scores between the exported graph
and eager execution, at resolutions that are deliberately **not** multiples of
32 — a regression that only appeared on non-standard aspect ratios is what
motivated that test.

---

## Testing

```bash
python -m pytest tests/ -q                       # 202 tests
cd faster_rcnn_gui && cargo test                 # 1,186 tests
cargo clippy --all-targets -- -D warnings        # clean
ruff check . --select=E,W,F --ignore=E501,E701,E702,E741,E401,E402
```

CI additionally builds the Docker image, runs `pip-audit` and Trivy, and
enforces the licensing gates described below.

---

## Licensing and legal

This section is not boilerplate. A copyright audit on 2026-07-21 examined the
dependency tree, the source for third-party code, the bundled binaries and the
model weights. Its findings are recorded here because most of them cannot be
recovered by reading the code.

### This project

Proprietary — see [`LICENSE`](LICENSE). Internal evaluation and research are
permitted; redistribution is not, without a separate agreement.

### Dependencies

**No strong copyleft in the code inventory.** All 664 Rust crates and 65 Python
packages (including transitive dependencies) were inventoried: zero GPL, AGPL,
SSPL, OSL, EUPL or CPAL. Nothing obliges this project to publish its source.

Three weak-copyleft components are present and unmodified: `certifi` (MPL-2.0),
`tqdm` (MPL-2.0 AND MIT), and `r-efi` (triple-licensed, MIT branch taken — and
a UEFI-only crate that does not link into the Windows build at all).

The complete inventory is [`THIRD_PARTY_NOTICES.md`](THIRD_PARTY_NOTICES.md),
generated by `tools/gen_third_party_notices.py`. CI fails if it is stale or if a
strong-copyleft dependency ever enters the tree.

**The inventory covers code dependencies, not the whole deployment.** Two things
it cannot see, recorded here so the sentence above is not read too widely:

- The optional Docker stack in `docker-compose.yml` pulls **Grafana**
  (AGPL-3.0) and **Redis 7.4** (RSALv2/SSPLv1 — Redis left BSD at 7.4). Both run
  as separate, unmodified processes, so neither reaches this project's source.
  But redistributing that stack makes you their distributor, and offering it as
  a managed service is what RSALv2 forbids. Neither is required to run the
  application.
- `torch` wheels built for CUDA bundle ~22 **NVIDIA** redistributables
  (`cudnn64_9.dll`, `cublas64_12.dll`, …) that carry the CUDA EULA and cuDNN
  SLA regardless of PyTorch's own BSD-3 licence. CPU wheels do not.

### Before distributing a binary

Three obligations attach to the executable itself, and none are satisfied by
shipping the `.exe` alone. Two are now discharged by
[`licenses/`](licenses/README.md), which holds the publishers' texts verbatim
and is gated by `tests/test_license_compliance.py`:

1. **ONNX Runtime 1.22.0 is linked statically** into `faster_rcnn_gui.exe` —
   there is no DLL beside the executable to make the dependency visible, but
   shipping the `.exe` distributes ONNX Runtime (MIT) all the same. Its
   `LICENSE` and `ThirdPartyNotices.txt` ship in `licenses/`. ✔
2. **`DirectML.dll` 1.15.4 is proprietary Microsoft software**, downloaded at
   build time by the `ort` crate's `directml` feature. Microsoft's terms
   require the license text to travel with it; it ships in `licenses/`. ✔
   Building without the `directml` feature removes the obligation entirely —
   set `FRCNN_NO_DIRECTML=1` so the tests agree.
3. **`faster_rcnn_gui/assets/icon.ico` has unrecorded provenance.** It may be
   original work or may come from an icon set requiring attribution. Until that
   is established, binary distribution is blocked. See
   [`assets/ATTRIBUTION.md`](faster_rcnn_gui/assets/ATTRIBUTION.md). *(The
   2026-07-22 audit found pixel evidence pointing strongly at original work —
   three sizes, exactly two distinct RGB values each, zero anti-aliasing, which
   is the signature of a drawn rectangle rather than a rasterised vector asset.
   Not yet closed out.)*

Refresh or verify the bundled texts with:

```bash
python tools/fetch_binary_licenses.py --check
```

### Model weights carry conditions that code licenses do not

This is the finding most likely to be missed. `torchvision` is BSD-3-Clause, but
that says nothing about the data its weights were trained on:

- **ImageNet backbones are loaded by default** (`pretrained_backbone=True`). The
  ImageNet terms of access are widely read as limiting use to non-commercial
  research, and whether that restriction reaches a trained network is unsettled.
  Pass `pretrained_backbone=False` (or `--no_pretrained`) to build without them.
- **COCO detection weights** were trained on Flickr photographs that COCO does
  not own; the annotations are CC BY 4.0 and require attribution.

For internal research both are low risk. Before selling a product that embeds
either, get legal review or retrain on data you control. Full detail is in
[`NOTICE`](NOTICE) section 3.

### Third-party code in this repository

One function is adapted rather than independently written, and it exists in two
places because the scriptable export path cannot call into the eager module:
`AnchorGenerator._make_anchors` in `model.py` and the base-anchor block of
`_gen_anchors` in `scriptable_export.py`, both from torchvision's
`anchor_utils.py` (BSD-3-Clause). Both are attributed in place.

*The second copy went unattributed until 2026-07-22, because the test enforcing
the attribution only looked at `model.py`. It now checks both, and additionally
fails on any other file carrying the adaptation's fingerprint without a notice —
the guard's scope had silently become the guarantee's scope.*

An audit of the
other detection, loss, augmentation, evaluation and export code found no
copied implementations — in particular the Mosaic/MixUp augmentation and the
YOLO-format exporters are independent work, so **no AGPL obligation from
Ultralytics attaches to this project**.

### Privacy

The webcam feature captures frames to a temporary file for inference. Detection
results, which may include `person` boxes, are written to session and export
files in plain text with no retention policy. If you deploy this where it
processes images of people, you are the data controller: set `API_KEY`, define
a retention period, and tell your users. Nothing is transmitted to the authors —
there is no telemetry, and the only outbound network calls are the Hugging Face
searches and downloads you trigger yourself.

---

## Project layout

```
model.py                 Faster R-CNN: backbone, FPN, RPN, RoI heads, anchors
model_cascade.py         Cascade R-CNN
model_fcos.py            FCOS (anchor-free)
losses.py                IoU-family losses, Soft-NMS, WBF
dataset.py               COCO and VOC readers
augmentation.py          Mosaic, MixUp, CutOut, geometric jitter
train.py                 training loop, DDP, AMP, EMA, schedules
eval_coco.py             COCO and VOC evaluation
calibration.py           temperature/Platt scaling, ECE
inference.py             single-image inference
tta_inference.py         test-time augmentation
export_model.py          ONNX / TorchScript export
scriptable_export.py     scriptable graph used by ONNX export
server.py                FastAPI inference server
model_registry.py        versioned checkpoint registry with provenance
active_learning.py       uncertainty sampling
distributed.py           DDP helpers
tools/                   notice generation and other maintenance scripts
tests/                   pytest suite
faster_rcnn_gui/         Rust + egui desktop client
monitoring/              Prometheus and Grafana configuration
```
