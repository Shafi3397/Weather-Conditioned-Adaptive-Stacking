# Weather-Conditioned Adaptive Stacking (WCAS) for Hourly Temperature Forecasting

Python implementation of **Weather-Conditioned Adaptive Stacking (WCAS)**, developed for an Advanced Machine Learning master's course assignment and accompanying IEEE-style conference-paper submission to the course instructor.

> **Status:** Coursework/research implementation. This repository does not claim peer-reviewed publication or externally verified results. The cleaned script has not yet been executed end-to-end on the original dataset; rerun and compare all reported metrics with the submitted manuscript.

## Overview

WCAS forecasts NASA POWER 2-m air temperature (`T2M`) one hour ahead by combining five regression experts: Random Forest, support vector regression (SVR), XGBoost, LightGBM, and CatBoost. Weather-conditioned LightGBM models estimate each expert's absolute forecast error, and inverse-error weighting dynamically combines their predictions.

## Experiment design

- **Task:** hourly `T2M(t+1)` regression (configurable forecast horizon `H`).
- **Input:** NASA POWER hourly meteorological observations with the fields required by `src/wcas.py`.
- **Features:** hourly weather variables; lagged observations; rolling summaries; calendar/target-hour encodings.
- **Split:** chronological training/testing split (last 20% held out), with a purge at the boundary.
- **Training:** five base regressors and expanding-window out-of-fold (OOF) predictions; a second time-series CV tunes inverse-error weight sharpness.
- **Baselines:** Random Forest, XGBoost, LightGBM, CatBoost, constrained weighted voting, and Ridge stacking. SVR is an internal ensemble expert; persistence is a diagnostic reference.
- **Ablations:** weather features removed, seasonal terms removed, static weights, and hard expert selection.
- **Evaluation:** MAE, RMSE, R², bias, hot/moderate/cool RMSE and exploratory statistical diagnostics.

## Repository structure

```text
Weather-Conditioned-Adaptive-Stacking/
├── README.md
├── requirements.txt
├── .gitignore
├── data/                 # put Combined.csv here (not committed)
├── src/
│   └── wcas.py
├── figures/              # generated PNG plots (not committed)
└── results/              # generated CSV summaries (not committed)
```

## Setup

Python 3.10 or newer recommended.

```bash
python -m venv .venv
# Windows PowerShell:
.venv\Scripts\Activate.ps1
# macOS/Linux:
# source .venv/bin/activate
python -m pip install -r requirements.txt
```

## Dataset

The original script referenced a Kaggle-mounted file under `shafiuzzamansaikat/wind-and-solar/Combined.csv`. Obtain the dataset from its original authorized source and place it in `data/Combined.csv`. Dataset redistribution rights have not been verified, so the CSV is intentionally excluded from this repository. Check the NASA POWER data-use/citation guidance and any applicable Kaggle dataset terms.

Required timestamp columns: `YEAR`, `MO`, `DY`, `HR`. Required meteorological columns are validated at runtime in the script. Input time stamps should be consecutive or interpretable as hourly records in the dataset's specified timezone.

## Run

From the repository root:

```bash
python src/wcas.py
```

To use a custom dataset path:

**Windows PowerShell:**
```powershell
$env:DATA_PATH = "C:\path\to\Combined.csv"
python src/wcas.py
```

**macOS/Linux:**
```bash
DATA_PATH=/path/to/Combined.csv python src/wcas.py
```

Optional variables `OUT_DIR` and `FIG_DIR` change output directories. The default output CSV files are written to `results/`, and plots to `figures/`.

## Outputs

| File | Description |
|---|---|
| `results/all_results.csv` | All models, including reference and ablations |
| `results/metrics_mae_rmse_r2.csv` | Main model comparison |
| `results/significance.csv` | Exploratory block-bootstrap and Wilcoxon comparison |
| `figures/*.png` | EDA, comparisons, ablation and residual plots |

No numerical results are supplied here because the dataset and validated execution output were not provided.

## Reproducibility considerations

1. **Preprocessing:** The provided original notebook used interpolation and a backward-fill operation on the full timeline, which can introduce future information. The published-cleaned version uses bounded *forward-fill only* before feature engineering, so resulting scores may differ from the submitted version. This change should be disclosed rather than silently assumed equivalent.
2. **Statistical comparison:** The strongest baseline is selected on the test set and then compared with WCAS on that same test set. Therefore, its confidence interval and p-value are exploratory/post-selection, not confirmatory evidence of significance.
3. **Time and hardware:** Full training includes repeated cross-validation of multiple ensembles and may be computationally demanding. Versions and computational environment affect reproducibility.
4. **Artifacts:** Existing plots are generated from the full series for exploratory description; they should not be represented as train-only feature selection.
5. **Scientific agreement:** Check final results and methodological details against the manuscript before tagging a release.

## Academic context

This project was created as an Advanced Machine Learning master's coursework assignment and used to prepare an IEEE-style conference-paper manuscript submitted to the course instructor. It is **not** described as accepted or published at an IEEE conference.

## Citation

If using this implementation, cite the repository URL and the corresponding paper if and when a public, citable version is available. Add the actual authors, year and manuscript title once confirmed.

## License

No open-source license has been assigned yet. Add an appropriate license only after confirming that the code and included materials can be released under that license.
