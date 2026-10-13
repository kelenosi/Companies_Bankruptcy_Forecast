# Project 1 — Reproducibility Guide

## Overview

This directory contains the report and supporting documentation for Project 1: **Predicting Corporate Bankruptcy Two Years Ahead**.

The project uses the Polish Companies Bankruptcy dataset from the UCI Machine Learning Repository to evaluate supervised classification methods for predicting corporate bankruptcy two years before the event.

Six classification algorithms are compared, followed by preprocessing sensitivity analysis and expanded hyperparameter tuning of Gradient Boosting.

**Dataset:** https://archive.ics.uci.edu/dataset/365/polish+companies+bankruptcy+data

**Repository:** https://github.com/kelenosi/Companies_Bankruptcy_Forecast.git

## 1. Repository Structure

The project assumes the following directory structure:

```text
Companies_Bankruptcy_Forecast/
├── README.md
├── requirements.txt
├── data/
│   ├── description.txt
│   └── processed/
│       └── bankruptcy_2yr.csv
├── notebooks/
│   ├── 00_data_import.ipynb
│   ├── 01_data_exploration.ipynb
│   └── 02_model_comparison.ipynb
├── results/
│   ├── tables/
│   └── figures/
└── reports/
    ├── README.md
    ├── Project_1_Bankruptcy_Prediction.qmd
    └── Project_1_Bankruptcy_Prediction.pdf
```

The notebook and report code assumes that `data/`, `notebooks/`, `results/`, and `reports/` are located under the same repository root.

The `results/tables/` directory contains exported CSV files used to reproduce the report's numerical results. The `results/figures/` directory contains exported visualizations.

## 2. Software Dependencies

### Python

The project uses Python 3 and Jupyter notebooks.

Core libraries include:

- pandas and NumPy — data preparation and analysis
- scikit-learn — preprocessing, model training, cross-validation, and evaluation
- Matplotlib — visualization
- SciPy — statistical analysis
- ucimlrepo — retrieval of the original UCI dataset
- Jupyter — notebook execution

Install the Python dependencies from the repository root:

```bash
python -m pip install -r requirements.txt
```

The `requirements.txt` file should contain the actual packages used by the notebooks and Quarto document.

### Quarto and PDF Rendering

The final report is authored using **Quarto** (`.qmd`).

Quarto is a separate application and is not installed by `pip install -r requirements.txt`.

Install Quarto from:

https://quarto.org/docs/get-started/

PDF rendering also requires a LaTeX distribution. Quarto supports TinyTeX, which can be installed using:

```bash
quarto install tinytex
```

The Quarto document executes Python code to generate report tables and figures. The Python environment used by Quarto must therefore have the required packages installed.

## 3. Dataset and Column Definitions

The dataset originates from the UCI Polish Companies Bankruptcy collection.

The original collection contains financial information for 43,405 company observations across five prediction horizons.

This project uses the fourth dataset (`year == 4`), corresponding to bankruptcy prediction two years in advance.

The resulting modeling dataset contains:

| Attribute                 | Value     |
| ------------------------- | --------- |
| Observations              | 9,792     |
| Financial predictors      | 64        |
| Bankrupt observations     | 515       |
| Non-bankrupt observations | 9,277     |
| Target variable           | `class`   |
| Prediction horizon        | Two years |

### Column Naming Convention

The UCI dataset describes the financial ratios using identifiers such as `Attr1`, `Attr2`, ..., `Attr64`.

The processed dataset uses shortened column names:

| Original identifier | Processed column |
| ------------------- | ---------------- |
| Attr1               | A1               |
| Attr2               | A2               |
| ...                 | ...              |
| Attr64              | A64              |

The mapping is positional: `A1` corresponds to the first financial ratio, `A2` to the second, and so forth.

The target column `class` indicates bankruptcy status:

- `0` — non-bankrupt
- `1` — bankrupt

**Financial ratio definitions:** Refer to `data/description.txt` for the source definitions and descriptions of the 64 attributes. This file should be treated as the reference for interpreting financial ratios, rather than attempting to infer their meanings from their numerical identifiers.

The processed dataset is stored at:

`data/processed/bankruptcy_2yr.csv`

## 4. Reproducing the Analysis

Execute the notebooks in the following order.

### Step 1 — Data Import

`notebooks/00_data_import.ipynb`

Retrieves the UCI dataset, selects the two-year prediction horizon, prepares the column structure, and exports the processed dataset.

### Step 2 — Exploratory Data Analysis

`notebooks/01_data_exploration.ipynb`

Examines:

- Class imbalance
- Missing values and class-dependent missingness
- Financial ratio distributions and outliers
- Correlations and redundant predictors
- Implications for preprocessing and model selection

### Step 3 — Model Comparison and Tuning

`notebooks/02_model_comparison.ipynb`

Performs:

- Stratified training/holdout split
- Five-fold cross-validation
- Comparison of six classification algorithms
- Hyperparameter optimization
- Preprocessing sensitivity analysis
- Gradient Boosting refinement
- Expanded 81-candidate Gradient Boosting grid search
- Threshold optimization using training out-of-fold predictions
- Final holdout evaluation

The expanded search evaluates 81 parameter combinations across five cross-validation folds, requiring **405 model fits** for the grid search alone.

Execution time depends on the available CPU and environment.

The notebook exports results to `results/tables/` and `results/figures/`.

## 5. Rebuilding the PDF Report

**The PDF can be regenerated without rerunning model training**, provided the exported CSV tables and required figure assets already exist.

The Quarto document reads previously generated results from:

`results/tables/`

To render the report, open a terminal at the repository root and execute:

```bash
quarto render reports/Project_1_Bankruptcy_Prediction.qmd --to pdf
```

The generated PDF will be placed in the `reports/` directory.

The report's Python code must be able to locate the repository's `results/tables/` and `results/figures/` directories. Its path configuration should match the directory structure shown in Section 1.

If the report references external image files, those images must also be present at their expected relative paths.

## 6. Evaluation Methodology

The modeling dataset is divided into stratified training (80%) and holdout (20%) subsets.

Hyperparameter selection uses five-fold stratified cross-validation on the training data.

Because bankrupt companies represent approximately 5.26% of observations, accuracy alone is not an adequate performance measure.

The principal evaluation metrics are:

- Average precision (PR-AUC)
- ROC-AUC
- Precision
- Recall
- F1 score

The final Gradient Boosting model is evaluated using both the default 0.50 classification threshold and a threshold selected using training out-of-fold predictions.

Threshold selection is performed on training predictions rather than the holdout labels.

## 7. Reproducibility Considerations

Several factors may affect exact replication:

**Randomness:** Results depend on the random seeds, data splitting, and cross-validation configuration defined in the notebooks.

**Software versions:** Changes to Python or scikit-learn versions may affect fitted models and numerical results.

**Computational requirements:** The expanded Gradient Boosting search is substantially more expensive than generating the final PDF.

**Dataset characteristics:** The data contains missing values, extreme financial ratios, and substantial class imbalance. Preprocessing decisions are part of the model pipeline and should not be changed when attempting to reproduce the reported results.

**Existing outputs:** Rendering the report from saved CSVs reproduces its presentation, not the underlying model-fitting process. Full computational reproduction requires executing the notebooks.

## 8. Data Source and References

Zięba, M., Tomczak, S. K., and Tomczak, J. M. (2016). _Ensemble Boosted Trees with Synthetic Features Generation in Application to Bankruptcy Prediction_. Expert Systems with Applications, 58, 93–101.

UCI Machine Learning Repository. _Polish Companies Bankruptcy_. https://archive.ics.uci.edu/dataset/365/polish+companies+bankruptcy+data

Quarto Documentation. https://quarto.org/docs/
