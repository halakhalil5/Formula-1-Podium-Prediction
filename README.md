# F1 Podium Prediction — CSEN 903 Milestone 1

Predict, for every driver in a Formula 1 race, whether he finishes on the podium (top 3), using only what is known **after qualifying and before the race starts**.

| | |
|---|---|
| Target | `podium = 1` if `positionOrder <= 3` |
| Grain | one row per `(raceId, driverId)` |
| Data | the course's Kaggle F1 (Ergast) dataset, 14 tables, 1950–2024 |
| Modeling table | 24,588 rows: train 1950–2019 (22,455) · validation 2020–21 (779) · test 2022–24 (1,354) |

## Results (test set, 2022–2024)

| Model | PR-AUC (primary) | ROC-AUC | F1 (secondary) | Precision | Recall |
|---|---|---|---|---|---|
| Logistic regression (baseline) | 0.711 | 0.936 | 0.673 | 0.542 | 0.887 |
| **Random forest (selected)** | **0.725** | 0.942 | 0.662 | 0.648 | 0.676 |
| Gradient boosting | 0.723 | 0.937 | 0.680 | 0.641 | 0.725 |
| Shallow NN (MLP, 5-network average) | 0.706 | 0.937 | 0.679 | 0.591 | 0.799 |
| Rule: grid ≤ 3 (reference) | 0.647 | 0.914 | 0.649 | 0.650 | 0.647 |

Hyperparameters and F1 thresholds were chosen on validation only. Every model beats the grid rule. None is significantly better than the baseline (race-level bootstrap).

## Repository layout

```
.
├── notebooks/
│   └── F1_Milestone1_Podium_Prediction.ipynb   # the single Run-All notebook (all deliverables)
├── reports/
│   └── F1_Milestone1_Report.pdf                 # analytical report (exported from the team doc)
├── requirements.txt
└── README.md
```

## Notebook sections

| # | Section | Deliverable |
|---|---|---|
| 1–4 | Setup, load, audit, EDA before cleaning | audit, EDA |
| 5 | Cleaning: placeholders, typing, keys, decisions D1–D14, quality gate | data cleaning |
| 6–8 | EDA after cleaning, saved tables, decision summary | EDA |
| 9 | Data-engineering questions Q1–Q3 | answers + figures |
| 10–12 | Feature engineering, leakage gate, time split, feature evidence | features, split |
| 13 | Metrics, imputation, preprocessing-order experiments, tuning, model comparison | modeling |
| 14 | Feature-group ablation, split (a) vs (b), cleaning-decision experiments | experiments |
| 15 | Permutation importance, SHAP, LIME | XAI |
| 16 | `predict_podium(record)` on a complete and a partially-missing record | inference |

## How to run

**Kaggle (recommended):** create a notebook, add the course's F1 dataset as input, upload `notebooks/F1_Milestone1_Podium_Prediction.ipynb`, then **Run All**. The notebook finds `circuits.csv` under `/kaggle/input` by itself. A full run takes about 15 minutes on CPU.

**Locally:**

```bash
pip install -r requirements.txt
export F1_DATA_DIR=/path/to/folder/with/the/csv/files
jupyter nbconvert --to notebook --execute notebooks/F1_Milestone1_Podium_Prediction.ipynb --output executed.ipynb
```

Cleaned tables and the feature table are written to `clean/` (or `/kaggle/working/clean` on Kaggle).

Random seeds are fixed. Tree and neural-network scores can shift slightly between scikit-learn versions; the setup cell of section 13 prints the version used. Tested with scikit-learn 1.6.1 (Kaggle) and 1.9.1.

## Key decisions

| ID | Decision |
|---|---|
| D2 | Shared cars: one driver per car (the one who drove it last); the row keeps the car's grid (TA-approved) |
| D3 | Non-starters removed (no grid slot at the cutoff) |
| D4 | Pit-lane starts placed behind the last grid slot + flag |
| D5 | Indianapolis 500 (1950–60) excluded |
| D7 | All seasons from 1950 used; missing qualifying filled from grid + `has_quali` flag (TA) |
| D9 | Mechanical retirement = any car-component failure, including punctures, tyres, fire and safety belt |
| D14 | Qualifying feature = % gap to the fastest Q1 time (confirmed by experiment) |

The full list, with evidence, is in notebook section 8 and in the report.

## Team

<!-- names and student IDs -->
