# CreditBridge: Explainable and Auditable Credit-Risk Scoring

CreditBridge is a framework that predicts loan default, explains the predictions, and assesses whether outcomes differ across groups. It is applied to two complementary datasets that are analyzed separately and never pooled.

**Status:** CRISP-DM Phases 1 to 4 complete (business understanding, data understanding, data preparation, modeling). Phase 5 (evaluation: explainability and fairness audit) and Phase 6 (deployment) are in progress.

## Datasets

| | LendingClub | Home Credit |
|---|---|---|
| File | `LC_loans_granting_model_dataset.csv` | `application_train.csv` |
| Source | Zenodo record 11295916 (v0.1, 25 May 2024), derived from the Kaggle LendingClub release | Kaggle: datuman/home-credit-default-risk-train-data-tabular (user-hosted copy of the Home Credit Default Risk competition data) |
| Access date | 2026-09-19 | 2026-09-19 |
| Records x columns (raw) | 1,347,681 x 15 | 307,511 x 122 |
| Target | `Default` (1 = charged off): 19.98% | `TARGET` (1 = payment difficulty): 8.07% |
| Licence | **[Creative Commons Attribution 4.0 International]** |

Raw files are **not** included in this repository, because redistribution terms
have not been confirmed. Download them from the sources above.
LendingClub md5 (from Zenodo): `b019384d6bc65bf2a3e839362e4ff502`.

## Repository structure

| Folder | Contents |
|---|---|
| `notebooks/` | `01_ingestion_eda`, `02_selection_evidence`, `03_preparation`, `04_modeling` |
| `data/` | **[final datasets `lc_final` and `hc_final` as parquet or csv.gz, plus `data_dictionary_lc.csv` and `data_dictionary_hc.csv`]** |
| `logs/` | `cleaning_log.csv`, `construction_log.csv`, `formatting_log.csv`, `hc_screen.csv`, `lc_roles.json`, `hc_roles.json`, `final_roles.json` |
| `results/` | `final_model_results.csv`, `final_lc_stability.csv`, `final_parameter_log.json`, `parameter_table_full.csv`, `tuning_results.csv`, `lr_tuning.csv`, logistic regression coefficients, decision tree rules, permutation importance files |

## How to reproduce

1. Download the two raw files and place them in the working directory (not committed).
2. Run the notebooks in order: `01_ingestion_eda`, `02_selection_evidence`,
   `03_preparation` (writes the cleaned, built and final datasets), `04_modeling`.
3. The notebooks read and write files in the working directory. If you run them
   from `notebooks/`, copy the JSON and data files there first or adjust the paths.

**Environment:** Python, pandas, numpy, scikit-learn 1.8.0, pyarrow. Random seed: 42.

## Method summary

| Step | Decision |
|---|---|
| Selection | LendingClub: 7 predictors of 15 variables. Home Credit: 12 predictors of 122 columns. Protected and geographic attributes held out for the fairness audit |
| Cleaning | No records dropped. Placeholders recoded, extreme values capped at the 99.9th percentile, flags added |
| Final datasets | LendingClub 1,347,681 x 15 (10 predictors). Home Credit 307,511 x 27 (18 predictors) |
| Split | LendingClub time-aware: train through 2014, validate 2015, test 2016 onward. Home Credit stratified 60/20/20 |
| Models | Logistic regression, decision tree, gradient boosting (class-weighted) |
| Metrics | ROC AUC, PR AUC, paired bootstrap |

## Phase 4 results (test set)

| Dataset | Model | ROC AUC | PR AUC | No-skill PR |
|---|---|---|---|---|
| LendingClub | Gradient boosting | 0.668 | 0.355 | 0.224 |
| | Logistic regression | 0.666 | 0.352 | |
| | Decision tree | 0.653 | 0.338 | |
| Home Credit | Gradient boosting | 0.744 | 0.228 | 0.081 |
| | Logistic regression | 0.737 | 0.220 | |
| | Decision tree | 0.719 | 0.198 | |

## Limitations

- LendingClub contains accepted, resolved loans only, so rejected applicants are absent.
- Neither dataset contains race or ethnicity. LendingClub supports geographic analysis only.
- Model scores rank risk and are not calibrated probabilities.
- Findings are associations, not causal effects.

## Author

Moyosola Fadiya. Applied Data Science Project. Nicholas Nardi. Fall 2026.
