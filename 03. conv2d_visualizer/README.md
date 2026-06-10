# Conv2d Visualizer Pro v9.0

A professional-grade CNN Conv2d layer design, analysis, and visualization tool built entirely in Rust with egui. Single-file architecture (~8,400 lines), zero external ML dependencies, compiles to a standalone ~5.4 MB executable.

## Screenshots

Dark-themed professional UI with 35 tabs organized into 7 groups.

## Features

### 35 Tabs in 7 Groups

| Group | Tabs | Description |
|-------|------|-------------|
| **CORE** | Calculator, Flow, Receptive Field, Compare, Sweep, Bottleneck | Output size/params/FLOPs/memory calculation, data flow diagrams, 4-config side-by-side comparison, parameter sweep, bottleneck analysis |
| **VISUAL** | Kernel Heatmap & FFT, Feature Map, Animation, ERF, Weight Viz | Kernel weight heatmaps with FFT frequency response, step-by-step convolution animation, effective receptive field heatmap, weight distribution visualization |
| **ARCH** | Layer Stack, Architecture, Arch Compare, Model Summary, Model Import, Fusion View | Multi-layer builder, architecture presets, torchinfo-style summary, Conv+BN+ReLU fusion pattern analysis |
| **PERF** | Memory Profile, Roofline, HW Profile, Latency Profile | Memory bandwidth analysis, roofline performance model, hardware-specific latency estimates |
| **TOOLS** | Code Gen, Export, Quantize, Prune, Sensitivity, Throughput | Multi-framework code generation, quantization simulation, pruning strategies, sensitivity analysis |
| **AI** | AI Diff, AI Ident, AI Tool | Conv characteristic classification, architecture pattern recognition, auto-optimization suggestions |
| **v9.0 NEW** | Training, GradFlow, Depth Decompose, TensorRT Export | LR/epoch/scheduler visualization, gradient flow analysis, depthwise decomposition, TensorRT export |

### Conv2d Variants

- Standard Convolution
- Depthwise Separable
- Pointwise (1x1)
- Dilated (Atrous)
- ConvTranspose2d (Deconvolution)

### Padding, Activation & Precision

- **Padding modes**: zeros, reflect, replicate, circular
- **Activations**: ReLU, GELU, Sigmoid, Tanh, SiLU, LeakyReLU
- **Precision**: FP32, FP16, INT8, BF16

### Code Generation

Generates layer code for:
- PyTorch
- TensorFlow / Keras
- JAX / Flax
- ONNX
- TensorRT
- YAML config

### Export Formats

- Clipboard copy
- File save: Text, CSV, JSON, YAML

### Keyboard Shortcuts

- `Ctrl+1` through `Ctrl+9` for quick tab switching

## Tech Stack

| Component | Version |
|-----------|---------|
| Rust | 2021 edition |
| eframe | 0.34 |
| egui | 0.34 |
| egui_plot | 0.35 |
| rand | 0.9 |
| arboard | 3 (clipboard) |
| image | 0.25 (icon loading) |

No Python, no ONNX runtime, no external ML libraries. All calculations are implemented in pure Rust.

## Build

```bash
# Debug build
cargo build

# Release build (optimized, ~5.4 MB)
cargo build --release
```

The release binary is located at `target/release/conv2d_visualizer.exe`.

Release profile is configured with LTO, single codegen unit, and symbol stripping for minimal binary size.

## Tests

194 unit tests covering core calculations, edge cases, and all tab logic.

```bash
cargo test
```

## Project Structure

```
conv2d_visualizer/
  Cargo.toml
  build.rs            # Windows resource embedding
  icon.ico            # Application icon
  src/
    main.rs           # Application (~8,400 lines, single-file)
    qc_tests.rs       # 194 unit tests
```

## System Requirements

- Windows 10/11 (GPU-accelerated via OpenGL/glow backend)
- No runtime dependencies

## License

Copyright (c) 2026 lightgo (lightgo1230@gmail.com)

Licensed under either of:

- [MIT License](https://opensource.org/licenses/MIT)
- [Apache License 2.0](https://www.apache.org/licenses/LICENSE-2.0)

at your option.
