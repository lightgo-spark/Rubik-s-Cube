# ClusterIQ v1.0

> Professional Document Embedding & Clustering Analytics Platform

**© LightGo** | lightgo1230@gmail.com

---

## Overview

ClusterIQ is a high-performance desktop application built with **Rust + egui** for document embedding, K-Means clustering, and multi-dimensional data analysis. It provides 25+ pandas-style visualization tabs, real-time streaming monitoring, multi-tenant management, and enterprise-grade alert/SLA tracking — all in a single lightweight executable.

---

## System Requirements

| Item | Minimum | Recommended |
|------|---------|-------------|
| OS | Windows 10 64-bit | Windows 11 64-bit |
| RAM | 4 GB | 8 GB+ |
| CPU | 4-core | 8-core+ |
| Storage | 50 MB | 200 MB+ |
| GPU | Not required | Not required |

---

## How to Run

```
embedding_cluster_gui.exe
```

No installation required. Double-click the `.exe` to launch.

---

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+G` | Generate sample data |
| `Ctrl+E` | Run embeddings |
| `Ctrl+K` | K-Means clustering |
| `Ctrl+L` | Elbow analysis |
| `Ctrl+Z` | Undo |
| `Ctrl+Y` | Redo |
| `Ctrl+S` | Save session |
| `Ctrl+C` | Copy results |
| `F1` | Keyboard shortcuts help |
| `ESC` | Close modal |

---

## Feature Overview

### Data Input

| Tab | Description |
|-----|-------------|
| 📁 CSV Upload | Load CSV / Excel files for analysis |
| 🌐 Data API | Connect to REST API endpoints |
| ⚡ Streaming | Real-time Kafka streaming with status bulbs |

### Clustering & Visualization

| Tab | Description |
|-----|-------------|
| 🔵 2D Scatter | PCA 2D cluster scatter plot |
| 🌐 3D View | PCA 3D interactive visualization |
| 📊 Parallel Coords | Parallel coordinates for cluster comparison |

### Pandas-style File Analysis (18 sub-tabs)

| # | Tab | pandas equivalent |
|---|-----|------------------|
| 0 | 📋 Overview | df.info() + df.head() |
| 1 | 🔗 Correlation | df.corr() heatmap |
| 2 | 📦 Boxplot | df.boxplot() |
| 3 | 🔵 Scatter | df.plot.scatter() |
| 4 | 📈 CDF | Cumulative distribution |
| 5 | 📐 Describe | df.describe() |
| 6 | 🎻 Violin | Violin + IQR box |
| 7 | 🔲 Pairplot | pd.plotting.scatter_matrix() |
| 8 | 〰 KDE | df.plot.kde() |
| 9 | 📊 Groupby | df.groupby().agg() |
| 10 | ⚡ Z-score | Outlier detection |
| 11 | 🔁 ACF | Autocorrelation function |
| 12 | 📊 Histogram | df.hist() |
| 13 | 🗺 Missing Map | df.isnull() heatmap |
| 14 | 📉 Rolling Stats | df.rolling().mean/std |
| 15 | 🥧 Pie Chart | df.groupby().pie() |
| 16 | 📏 Q-Q Plot | Normal quantile-quantile |
| 17 | 📐 PACF | Partial autocorrelation |

### Monitoring

| Tab | Description |
|-----|-------------|
| 🔔 Alerts & SLA | System health bulbs + SLA progress bars |
| 🏢 Tenant Mgmt | Multi-tenant management & quota |

---

## Streaming Status Bulbs

The Streaming tab displays 4 real-time status light bulbs:

| Bulb | Green | Yellow | Red |
|------|-------|--------|-----|
| **Connect** | Running | Paused | Stopped |
| **Errors** | No errors | — | Errors detected |
| **Latency** | < 20ms | 20–50ms | > 50ms |
| **Throughput** | > 800/s | 400–800/s | < 400/s |

---

## Alerts & SLA Bulbs

| Bulb | Meaning |
|------|---------|
| **System** | Overall system health |
| **SLA** | SLA compliance (≥99.5% green, ≥95% yellow, below red) |
| **Info** | Informational alerts active |
| **Warn** | Warning alerts active |
| **Critical** | Critical/error alerts active |

---

## Contact & Support

- **Developer:** LightGo
- **Email:** lightgo1230@gmail.com

---

*ClusterIQ v1.0 — Built with Rust + egui*

## Dataset Attribution & License

This project utilizes the **top_repositories_github** dataset developed by **Sohaib Malik**.

### License Notice
The dataset is used in compliance with the Apache License, Version 2.0 (the "License"). You may not use this dataset except in compliance with the License. A copy of the License is available at:
http://www.apache.org/licenses/LICENSE-2.0
