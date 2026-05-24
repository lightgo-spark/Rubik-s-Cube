# DL Explorer GraphData

This folder contains sample graph data files representing the types of data produced by each visualization tab in **Deep Learning Explorer Pro v3.0**.

---

## What This Folder Is

Each subfolder corresponds to a specific module/tab in the application. The CSV files inside contain **realistic sample data** that illustrates the shape and structure of the data those tabs generate and visualize. These files are useful for:

- Understanding the output format before running the app
- Testing downstream analysis scripts or Excel charts
- Using as reference schemas for custom data import

---

## How to Use the Data Files

1. Open any `.csv` file in Excel, Python (pandas), R, or any data tool
2. Each file has a header row describing the columns
3. Data values are realistic but synthetic — they demonstrate typical ranges and patterns for each algorithm
4. You can use these files as templates to format your own data for import into the app (where supported)

---

## Exporting Actual Data from the App

To export real experiment data from **Deep Learning Explorer Pro**:

1. Launch `DL_Explorer_Pro.exe`
2. Run any experiment in the relevant tab
3. Navigate to the **Settings** tab (last tab in the sidebar)
4. Click **Export Data** — results are saved in `.dlexp` format
5. An integrity log `dl_integrity.log` is automatically created on first launch

---

## Folder Structure

| Folder | CSV File | Description |
|--------|----------|-------------|
| 01_Activation/ | activation_curves.csv | Activation function output values over input range |
| 02_Loss/ | loss_curves.csv | Loss function values over prediction range |
| 03_Optimization/ | optimization_paths.csv | Optimizer trajectory steps on loss surface |
| 04_NN_Lab/ | training_history.csv | Per-epoch training and validation metrics |
| 05_Regression/ | regression_data.csv | Regression model predictions vs ground truth |
| 06_Classification/ | classification_metrics.csv | Per-model classification performance metrics |
| 07_LR_Finder/ | lr_loss_curve.csv | Loss vs learning rate sweep |
| 08_GAN/ | gan_training.csv | Generator and discriminator loss over steps |
| 09_Embedding/ | word_vectors.csv | 2D word vector projections |
| 10_BatchNorm/ | bn_distributions.csv | Feature distributions before/after batch normalization |
| 11_TimeSeries/ | time_series.csv | Time series values with moving average and forecast |
| 12_Anomaly/ | anomaly_scores.csv | Anomaly detection scores per sample |
| 13_ModelCompare/ | model_comparison.csv | Multi-model performance comparison |
| 14_Hyperparam/ | hyperparam_search.csv | Hyperparameter grid search results |
| 15_DataAnalysis/ | pca_tsne.csv | PCA and t-SNE 2D projections with labels |

---

© 2026 LightGo. Educational & Research Use.
