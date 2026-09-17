# AIoT-Based Multi-Horizon DO Forecasting and Decision Support for Fish Nursery Ponds

## Reproducibility Package — IJIES Submission No. 20265445

This repository contains the reproducibility package for the revised manuscript:

**Assessment-Guided Multi-Horizon LSTM for Dissolved Oxygen Risk Prediction in an AIoT Fish Nursery Decision Support System**

The package provides:
- the legacy revised experiment notebook used for the main manuscript analyses;
- the frozen-protocol PatchTST-only notebook added during the second review;
- raw and processed datasets plus split metadata;
- model-level result tables and uncertainty analyses;
- supporting figures; and
- configuration, leakage-audit, and reproducibility files.

---

## 1. Study scope

The study develops an **AIoT-based decision support system (DSS)** for fish nursery pond management using multi-horizon dissolved oxygen (DO) forecasting and temporal assessment context.

The framework combines:
- **IoT-based water-quality monitoring**;
- **temporal assessment features**: suitability, excursion, and stability;
- **multi-horizon DO forecasting** at 15, 30, 45, and 60 minutes; and
- **risk-aware DSS outputs** including current assessment, predicted low-DO risk, and operational recommendations.

The main proposed configuration is the **Full Assessment-Guided LSTM**.

During the second review, **PatchTST-lite** was added as a stronger contemporary comparator. The PatchTST experiment uses a **frozen protocol**: the legacy models were not retrained, the original preprocessing/splits/evaluation pipeline was preserved, and only the new comparator was added.

---

## 2. Repository structure

```text
.
├── README.md
├── config/
│   ├── environment_info.json
│   ├── feature_engineering_config.json
│   ├── feature_groups.json
│   ├── hyperparameter_search_log.csv
│   └── reproducibility_manifest.json
├── data/
│   ├── raw/
│   │   └── iot_data.csv
│   ├── processed/
│   │   ├── processed_iot_dataset_with_assessment.csv
│   │   └── processed_iot_dataset_with_features.csv
│   └── metadata/
│       ├── data_readiness_summary.csv
│       ├── interval_summary.csv
│       └── pond_calendar_span.csv
├── docs/
│   └── DESIGN_CHANGES_SUMMARY.md
├── figures/
│   ├── fig_ablation_rmse_with_uncertainty.png
│   ├── fig_fair_lstm_vs_gru.png
│   ├── fig_recall_lowdo_model_comparison.png
│   ├── fig_rmse_model_comparison.png
│   └── fig_threshold_sensitivity.png
├── notebooks/
│   ├── Revised_Design_Experiment.ipynb
│   └── PatchTST_Only_Frozen_Protocol_IJIES.ipynb
├── results/
│   ├── legacy/
│   │   ├── regression/
│   │   ├── low_do/
│   │   ├── decision/
│   │   ├── uncertainty/
│   │   ├── rolling_origin/
│   │   ├── event_based/
│   │   ├── threshold_analysis/
│   │   ├── prediction_audits/
│   │   ├── assessment/
│   │   ├── audits/
│   │   └── logs/
│   └── patchtst_lite/
│       └── patchtst_*.csv
└── splits/
    ├── fold0_test_timestamps.csv
    ├── fold1_test_timestamps.csv
    ├── fold2_test_timestamps.csv
    └── sequence_split_summary_all_folds.csv
```

---

## 3. Notebook roles

### `notebooks/Revised_Design_Experiment.ipynb`

Main legacy notebook used to generate the revised manuscript results before the PatchTST-lite addition. It contains:
- baseline and assessment-guided forecasting;
- low-DO sensitivity evaluation;
- decision-level metrics;
- uncertainty analysis;
- rolling-origin validation; and
- supporting diagnostic outputs.

### `notebooks/PatchTST_Only_Frozen_Protocol_IJIES.ipynb`

Second-review notebook used only to evaluate PatchTST-lite under the frozen protocol. It:
- preserves the legacy evaluation framework;
- evaluates only the PatchTST-lite comparator; and
- writes PatchTST-specific outputs under `results/patchtst_lite/`.

---

## 4. Main experiment groups

### 4.1 Legacy models

The legacy experiment set includes:
- Persistence baseline
- Raw LSTM
- Raw GRU
- Full Assessment GRU
- LSTM + Suitability
- LSTM + Excursion
- LSTM + Stability
- Full Assessment-Guided LSTM
- GRU-N-Beats-lite
- CNN-GRU-Attention

Legacy outputs are stored under `results/legacy/`.

### 4.2 Second-review comparator

The second-review addition is:
- **PatchTST-lite**

PatchTST outputs are stored under `results/patchtst_lite/`.

---

## 5. Result folders

- `results/legacy/regression/` — horizon-wise regression outputs.
- `results/legacy/low_do/` — low-DO sensitivity metrics.
- `results/legacy/decision/` — decision-level metrics.
- `results/legacy/uncertainty/` — multi-seed uncertainty and significance analyses.
- `results/legacy/rolling_origin/` — rolling-origin robustness results.
- `results/legacy/event_based/` — event-level low-DO detection.
- `results/legacy/threshold_analysis/` — threshold sensitivity and threshold-free AUC analyses.
- `results/legacy/prediction_audits/` — model-specific prediction audit files.
- `results/legacy/assessment/` — temporal assessment summaries.
- `results/legacy/audits/` — leakage-audit outputs.
- `results/legacy/logs/` — execution/runtime logs.
- `results/patchtst_lite/` — PatchTST-lite second-review outputs.

Representative PatchTST files include:
- `patchtst_regression_metrics_all_runs.csv`
- `patchtst_low_do_metrics_all_runs.csv`
- `patchtst_decision_level_metrics_all_runs.csv`
- `patchtst_rmse_uncertainty_fold0_multiseed.csv`
- `patchtst_recall_uncertainty_fold0_multiseed.csv`
- `patchtst_decision_accuracy_uncertainty_fold0_multiseed.csv`
- `patchtst_rmse_across_rolling_origin_folds.csv`
- `patchtst_event_based_metrics_primary_run.csv`
- `patchtst_threshold_sensitivity_primary_run.csv`
- `patchtst_threshold_free_auc_primary_run.csv`
- `patchtst_run_log.csv`

---

## 6. Figures

The `figures/` folder contains manuscript-supporting visualizations.

- `fig_rmse_model_comparison.png` — multi-horizon RMSE comparison, including PatchTST-lite.
- `fig_recall_lowdo_model_comparison.png` — multi-horizon low-DO recall comparison, including PatchTST-lite.
- `fig_ablation_rmse_with_uncertainty.png` — ablation analysis of assessment-guided LSTM variants.
- `fig_fair_lstm_vs_gru.png` — matched LSTM-versus-GRU comparison.
- `fig_threshold_sensitivity.png` — threshold sensitivity analysis.

---

## 7. Data and split files

### Raw data
- `data/raw/iot_data.csv`

### Processed data
- `data/processed/processed_iot_dataset_with_assessment.csv`
- `data/processed/processed_iot_dataset_with_features.csv`

### Exact split metadata
- `splits/fold0_test_timestamps.csv`
- `splits/fold1_test_timestamps.csv`
- `splits/fold2_test_timestamps.csv`
- `splits/sequence_split_summary_all_folds.csv`

These files preserve the split logic used for the reported experiments.

---

## 8. Reproducibility notes

This repository reflects two revision stages.

### Stage 1 — Major revision legacy package

The first revised package generated:
- the legacy benchmark outputs;
- the assessment-guided LSTM analyses;
- rolling-origin validation;
- uncertainty outputs; and
- decision-support diagnostics.

### Stage 2 — Second-review update

The second review required a stronger contemporary comparison. PatchTST-lite was therefore added under the same dataset, preprocessing, historical window, forecast horizons, split indices, scaling logic, and evaluation definitions.

The legacy models were **not retrained or retuned solely because PatchTST-lite was added**. This frozen-protocol design avoids unnecessary experimental drift and keeps the second-review comparator traceable.

---

## 9. How to reproduce

### Legacy experiment

1. Open `notebooks/Revised_Design_Experiment.ipynb` in Google Colab.
2. Use `data/raw/iot_data.csv` when the notebook requests the raw IoT dataset.
3. Run the quick-test mode first if enabled.
4. Run the full configuration to regenerate the legacy result package.

### PatchTST-lite second-review experiment

1. Open `notebooks/PatchTST_Only_Frozen_Protocol_IJIES.ipynb`.
2. Use the same raw IoT dataset and frozen experimental protocol.
3. Run with `QUICK_TEST_MODE = True` for a smoke test.
4. Set `QUICK_TEST_MODE = False`, restart the runtime, and run all cells.
5. Compare the generated outputs with `results/patchtst_lite/`.

---

## 10. Interpretation guidance

The repository is consistent with the final revised manuscript interpretation:

- **Raw LSTM** provides the lowest point-forecast RMSE.
- **PatchTST-lite** is a competitive contemporary Transformer-type comparator and provides strong timestamp-level low-DO sensitivity.
- **Full Assessment-Guided LSTM** remains the main integrative, assessment-informed configuration because it links suitability, excursion, and stability context with forecasting and decision-oriented DSS interpretation.

The manuscript therefore does **not** claim that one model dominates every metric. Its contribution is the integration of:
1. temporal assessment context;
2. multi-horizon DO forecasting; and
3. operational decision support.

---

## 11. Evaluation protocol notes

- Historical input window: **24 steps (6 hours)**.
- Forecast horizons: **15, 30, 45, and 60 minutes**.
- Primary split: chronological **70/15/15%** train/validation/test per pond.
- Primary-split uncertainty: **five seeds** (`42, 7, 123, 2024, 99`).
- Rolling-origin folds 1–2: **seed 42 only**.
- Scaling parameters are fitted on each fold's training partition only.
- PatchTST-lite uses the same **58 raw sensor + temporal features** used by the Raw LSTM/Raw GRU benchmark.

---

## 12. Contact and provenance

Prepared as the reproducibility package for **IJIES / INASS Submission No. 20265445**.

Repository maintainer and corresponding author:

**Firda Amalia**

If this package is used in academic work, please cite the corresponding final manuscript.
