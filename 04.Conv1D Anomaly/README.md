<div align="center">

# ⚡ Conv1D Anomaly Detection Studio v15.0

**26 Algorithms · 18 Tabs · 142 Tests · Single Binary · Zero Dependencies**

Built with **Rust** + **egui** — Fast, Safe, Native

---

*A desktop application for 1D time-series anomaly detection,  
featuring 26 algorithms from classical statistics to deep learning autoencoders.*

## ✨ Features

### 🧠 26 Anomaly Detection Algorithms
From classical statistics to neural network autoencoders — all implemented **from scratch in pure Rust**. No Python, ONNX, or external ML runtime needed.

### 🖥️ 18-Tab Professional UI
| Tab | Function | Tab | Function |
|-----|----------|-----|----------|
| **Data** | CSV import | **Export** | CSV/JSON/HTML/TXT reports |
| **Model** | Algorithm & hyperparameters | **Hub** | Model presets + URL download |
| **Training** | Loss curve + early stopping | **Stats** | Histogram + distribution |
| **Detection** | Score overlay + threshold | **AI** | Algorithm ranking |
| **Analysis** | ROC/PR/F1 + confusion matrix | **Streaming** | Real-time TCP socket |
| **Compare** | Multi-algorithm benchmark | **MultiVar** | Multi-channel correlation |
| **Forecast** | Time-series prediction | **Explain** | Feature attribution |
| **Batch** | Multi-file processing | **Drift** | Concept drift detection |
| **Plugin** | Custom formula parser |

### 📂 Smart CSV Import
- **Auto-delimiter detection** — comma, tab, semicolon
- **Multi-column selector** with checkboxes (Select All / Deselect All)
- **5 merge modes** — Average, Sum, Max, Min, RMS
- **Auto-imputation** — NaN/missing values handled automatically
- **Preprocessing** — Clip ±3σ, 1st Difference, Detrend, Smooth

### 📦 Binary, Zero Dependencies
- **8.4 MB** standalone `.exe` — no installation, no runtime, no GPU required
- **142 unit tests** with 0 warnings
- Runs on Windows 10/11

---

## 🔬 Algorithms (26)

### Deep Learning Autoencoders
| # | Algorithm | Description |
|---|-----------|-------------|
| 1 | **Conv1D-AE** | Convolutional autoencoder reconstruction error |
| 2 | **VAE** | Variational autoencoder with KL divergence |
| 3 | **GRU-AE** | Gated Recurrent Unit autoencoder |
| 4 | **LSTM-AE** | Long Short-Term Memory autoencoder |
| 5 | **Transformer-AE** | Multi-head self-attention autoencoder |

### Statistical Methods
| # | Algorithm | Description |
|---|-----------|-------------|
| 6 | **Z-Score** | Rolling window z-score thresholding |
| 7 | **MAD** | Median Absolute Deviation (robust z-score) |
| 8 | **Grubbs** | Grubbs' test for single outlier |
| 9 | **IQR** | Interquartile Range fence method |
| 10 | **KDE** | Kernel Density Estimation low-likelihood |

### Sequential & Control Charts
| # | Algorithm | Description |
|---|-----------|-------------|
| 11 | **CUSUM** | Cumulative Sum chart for mean shifts |
| 12 | **EWMA** | Exponentially Weighted Moving Average |
| 13 | **SPC** | Western Electric 4-rule control chart |
| 14 | **Hampel** | Sliding median ± k×1.4826×MAD filter |
| 15 | **Holt-Winters** | Triple exponential smoothing (seasonal) |

### Frequency & Multi-scale
| # | Algorithm | Description |
|---|-----------|-------------|
| 16 | **FFT** | Fast Fourier Transform spectral anomaly |
| 17 | **Spectral Residual** | Spectral saliency map |
| 18 | **Wavelet** | Haar DWT multi-level detail coefficients |

### Clustering & Density
| # | Algorithm | Description |
|---|-----------|-------------|
| 19 | **Isolation Forest** | Random partition path-length score |
| 20 | **DBSCAN** | Density-Based Spatial Clustering outliers |
| 21 | **LOF** | Local Outlier Factor k-NN density ratio |
| 22 | **One-Class SVM** | RBF kernel boundary detection |

### Other
| # | Algorithm | Description |
|---|-----------|-------------|
| 23 | **Mahalanobis** | Multivariate distance with window features |
| 24 | **Matrix Profile** | STOMP discord detection |
| 25 | **BOCPD** | Bayesian Online Changepoint Detection |
| 26 | **Robust PCA** | Low-rank + sparse decomposition |
| — | **Ensemble** | Multi-algorithm majority voting |

---

## 📸 Screenshots

> *Add screenshots here: Data tab, Detection tab, Analysis tab, Compare tab, etc.*

---

## 🚀 Quick Start

### Prerequisites
- **Rust toolchain** — Install from [rustup.rs](https://rustup.rs)
- **Windows 10/11** (primary target)
- No GPU required


## 📁 Supported CSV Formats

| Feature | Support |
|---------|---------|
| **Delimiters** | Comma `,` · Tab `\t` · Semicolon `;` (auto-detected) |
| **Header Row** | Optional (auto-detected) |
| **Column Count** | Any number of numeric columns |
| **Multi-Column** | Checkbox selection + 5 merge modes |
| **Missing Values** | Auto-imputed (linear interpolation) |
| **Normalization** | Z-Score, MinMax, Robust, None |
| **Preprocessing** | Clip ±3σ, 1st Difference, Detrend, Smooth |

### Example CSV
```csv
timestamp,temperature,pressure,vibration
2026-01-01 00:00,25.3,101.2,0.45
2026-01-01 00:01,25.7,101.1,0.48
2026-01-01 00:02,38.5,95.2,2.31
```

---

## 🏗️ Architecture

```
conv1d-anomaly-detection/
├── Cargo.toml          # Dependencies & build config
├── build.rs            # Windows icon & metadata (winres)
├── icon.ico            # Application icon
└── src/
    ├── main.rs         # 13,422 lines — entire application
    └── tests.rs        # 142 unit tests
```

### Tech Stack
| Component | Technology | Version |
|-----------|-----------|---------|
| Language | Rust | 2021 Edition |
| GUI | egui + eframe | 0.27 |
| Plotting | egui_plot | 0.27 |
| Parallelism | rayon | 1.8 |
| Serialization | serde + serde_json | 1.0 |
| File Dialog | rfd | 0.14 |
| HTTP | ureq | 2.0 |
| Date/Time | chrono | 0.4 |

All dependencies use **permissive licenses** (MIT, Apache-2.0, BSD-3-Clause, ISC, BSL-1.0).  
**No GPL/AGPL/copyleft** dependencies.

---


## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+1` ~ `Ctrl+0` | Switch to tab 1~10 |
| `Ctrl+-` / `Ctrl+=` | Stats / AI tab |
| `Ctrl+[` / `Ctrl+]` | Streaming / MultiVar tab |
| `Ctrl+\` / `Ctrl+;` | Explain / Drift tab |
| `Ctrl+,` / `Ctrl+`` | Plugin / About tab |
| `?` | Toggle shortcut legend |

---

## 📊 Performance

| Metric | Value |
|--------|-------|
| Source Lines | 15,516 (main.rs + tests.rs) |
| Binary Size | 8.4 MB (release, LTO) |
| Unit Tests | 142 passed, 0 failed |
| Compiler Warnings | 0 |
| Algorithms | 26 |
| UI Tabs | 18 |
| Signal Types | 12 |
| Domain Presets | 5 |
| External Runtime | None (standalone .exe) |
| GPU Required | No |

---

## 📄 License

© 2026 lightgo. All rights reserved.

Contact: lightgo1230@gmail.com
