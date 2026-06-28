# Beam Search Studio v19.0

Desktop visualizer for beam search and text decoding strategies, built with Rust + egui.

> Copyright (c) 2026 lightgo (lightgo1230@gmail.com)

---

## Overview

A desktop tool built to experiment with and compare LLM decoding strategies. Supports 19 strategies including beam search, nucleus sampling, contrastive search, and speculative decoding. Includes a built-in NanoTransformer for offline testing, with Ollama and HuggingFace API support for live inference.

Single executable (11.4 MB), no installation required.

## Decoding strategies (19)

| # | Strategy | Notes |
|---|----------|-------|
| 1 | Beam Search | length penalty, diversity bonus, n-gram blocking |
| 2 | Group Beam Search | per-group diverse beam decoding |
| 3 | Greedy | temperature-scaled argmax |
| 4 | Top-K Sampling | best-of-5 selection |
| 5 | Nucleus (Top-P) | cumulative probability cutoff |
| 6 | Contrastive Search | O(1) global frequency via HashMap |
| 7 | Typical Decoding | information-theoretic local filtering |
| 8 | Mirostat | adaptive surprise rate |
| 9 | Eta Sampling | entropy-conditional truncation |
| 10 | Best-of-N (DPP) | diversity-aware selection |
| 11 | MBR Decoding | pairwise BLEU consensus |
| 12 | Lookahead | entropy-adaptive depth |
| 13 | Min-P Sampling | relative probability floor |
| 14 | Speculative Decoding | draft + verify |
| 15 | Entropy Early Exit | dynamic stopping on low-entropy distributions |
| 16 | CFG | classifier-free guidance logit interpolation |
| 17 | Top-A Sampling | adaptive threshold |
| 18 | Quadratic Sampling | smoothing curve probability redistribution |
| 19 | XTC Sampling | threshold-based random cutoff |

## Tabs (50 total)

- Core: Generation, Tree View, Comparison, Step-by-Step
- Analysis: Heatmap, Histogram, Alignment, Convergence
- Benchmark: Profiler, Perf Dashboard, Strategy Toggle, Batch
- IO: Export, Engine Export, External Logits, Session Report, History, Prompt Library
- Ollama: Live, Chat, Compare, Embed, Token Stats
- Advanced: Sankey, Repetition, Competitive Matrix, Attention Sim, Strategy Radar
- v15+: HF Inference, Pipeline Builder, Attention Deep, Model Benchmark, Streaming View
- Domain: MT Analysis, Confidence, N-Best Export, Constraints Lab, FIM Mode
- Sys: Settings, Tutorial, Sentence Context

## Build

```bash
# Rust 1.75+ required
cargo build --release
./target/release/beam_search_studio.exe
```

## Tests

```bash
cargo test
# 107 passed, 0 failed, 0 warnings
```

## Benchmarks

Tested on 3,000 samples:

| Strategy | Score | Latency | TPS |
|----------|-------|---------|-----|
| Ensemble | 0.843 | 79.5ms | 573 |
| Best-of-N-DPP | 0.836 | 36.8ms | 1,208 |
| MBR Decoding | 0.806 | 54.4ms | 695 |
| Contrastive | 0.781 | 22.0ms | 1,570 |
| Beam Search | 0.777 | 41.4ms | 814 |
| Greedy | 0.715 | 5.2ms | 6,636 |

## Stack

| | |
|---|---|
| Language | Rust 2021 |
| GUI | egui 0.31 + eframe 0.31 |
| DB | rusqlite 0.32 (SQLite, WAL mode) |
| HTTP | ureq 2 |
| Serialization | serde + serde_json |
| RNG | rand 0.8 |

## Engine export

Generates config files for: HuggingFace Transformers, vLLM, TensorRT-LLM, Ollama Modelfile, llama.cpp, OpenAI API.

---

18,982 lines | 11.4 MB | 107 tests | 0 warnings
