# AIoT-Based Multi-Horizon DO Forecasting and Decision Support for Fish Nursery Ponds  
## Revised Results Reproducibility Package (IJIES Submission No. 20265445)

This repository contains the reproducibility package for the revised manuscript submitted to **INASS / IJIES**:

**“An interpretable AIoT framework for dissolved oxygen forecasting and decision support in fish nursery ponds”**  
(Title may follow the final manuscript wording.)

The package provides:
- the **legacy revised experiment notebook** used for the main manuscript analyses,
- the **frozen-protocol PatchTST-only notebook** added during second review,
- processed datasets and split metadata,
- result tables used in the manuscript,
- supporting figures,
- and configuration / audit files for reproducibility.

---

## 1. Study scope

The study develops an **AIoT-based decision support system (DSS)** for fish nursery pond management using multi-horizon dissolved oxygen (DO) forecasting and temporal assessment context.

The framework combines:
- **IoT-based water quality monitoring**
- **Temporal assessment features**
  - suitability
  - excursion
  - stability
- **Multi-horizon DO forecasting**
  - 15, 30, 45, and 60 min horizons
- **Risk-aware DSS outputs**
  - current assessment
  - predicted low-DO risk
  - alert / action recommendation

The main proposed configuration in the manuscript is:

- **Full Assessment-Guided LSTM**

During the **second review**, a stronger contemporary comparator was added:

- **PatchTST-lite**

Importantly, the PatchTST experiment was run under a **frozen protocol**:
- the original legacy models were **not retrained**,
- the original preprocessing / splits / evaluation pipeline were preserved,
- only the PatchTST-lite comparator was added under the same evaluation logic.

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


3. Notebook roles
notebooks/Revised_Design_Experiment.ipynb
This is the main legacy notebook used to produce the revised manuscript results before the PatchTST-lite addition. It contains the main experimental pipeline for:
baseline and assessment-guided forecasting,
low-DO sensitivity evaluation,
decision-level metrics,
uncertainty analysis,
rolling-origin validation,
and supporting diagnostic outputs.
notebooks/PatchTST_Only_Frozen_Protocol_IJIES.ipynb
This notebook was created specifically for the second review to add PatchTST-lite as a contemporary benchmark while preserving the original protocol.
It:
keeps the legacy evaluation framework fixed,
evaluates only the PatchTST-lite comparator,
outputs results under results/patchtst_lite/.


4. Main experiment groups
4.1 Legacy models
The legacy experiment set includes:
Persistence baseline
Raw LSTM
Raw GRU
Full Assessment GRU
LSTM + Suitability
LSTM + Excursion
LSTM + Stability
Full Assessment-Guided LSTM
GRU-N-Beats-lite
CNN-GRU-Attention
These outputs are stored under:
results/legacy/

4.2 Second-review comparator
Added in response to the editor’s second review:
PatchTST-lite
These outputs are stored under:
results/patchtst_lite/


5. Result folders
results/legacy/regression/
Stores horizon-wise regression outputs for the legacy experiment set.
Typical file:
regression_metrics_all_runs.csv
results/legacy/low_do/
Stores low-DO sensitivity metrics for the legacy models.
Typical file:
low_do_metrics_all_runs.csv
results/legacy/decision/
Stores decision-level metrics.
Typical file:
decision_level_metrics_all_runs.csv
results/legacy/uncertainty/
Stores uncertainty analyses from repeated primary-split runs (multi-seed).
Typical files:
decision_accuracy_uncertainty_fold0_multiseed.csv
rmse_uncertainty_fold0_multiseed.csv
recall_uncertainty_fold0_multiseed.csv
significance_tests_fold0_multiseed.csv
results/legacy/rolling_origin/
Stores rolling-origin validation outputs.
Typical file:
rmse_across_rolling_origin_folds.csv
results/legacy/event_based/
Stores event-level low-DO detection analysis.
Typical file:
event_based_metrics_primary_run.csv
results/legacy/threshold_analysis/
Stores threshold sensitivity and threshold-free discrimination metrics.
Typical files:
threshold_sensitivity_primary_run.csv
threshold_free_auc_primary_run.csv
results/legacy/prediction_audits/
Stores model-specific prediction audits.
Typical files:
prediction_audit_*.csv
results/legacy/assessment/
Stores temporal assessment summaries.
Typical files:
assessment_suitability_summary.csv
assessment_excursion_do_summary_with_prevalence.csv
assessment_temporal_stability_summary.csv
results/legacy/audits/
Stores audit files.
Typical file:
leakage_audit_result.json
results/legacy/logs/
Stores execution logs.
Typical file:
run_log_timing.csv
results/patchtst_lite/
Stores PatchTST-lite outputs generated under the frozen second-review protocol.
Typical files:
patchtst_regression_metrics_all_runs.csv
patchtst_low_do_metrics_all_runs.csv
patchtst_decision_level_metrics_all_runs.csv
patchtst_rmse_uncertainty_fold0_multiseed.csv
patchtst_recall_uncertainty_fold0_multiseed.csv
patchtst_decision_accuracy_uncertainty_fold0_multiseed.csv
patchtst_rmse_across_rolling_origin_folds.csv
patchtst_event_based_metrics_primary_run.csv
patchtst_threshold_sensitivity_primary_run.csv
patchtst_threshold_free_auc_primary_run.csv
patchtst_run_log.csv


6. Figures
The figures/ folder contains figure files used to support manuscript interpretation.
Current figures
fig_rmse_model_comparison.png
Multi-horizon RMSE comparison across the main benchmark models, including PatchTST-lite.
fig_recall_lowdo_model_comparison.png
Multi-horizon low-DO recall comparison across the main benchmark models, including PatchTST-lite.
fig_ablation_rmse_with_uncertainty.png
Ablation-style analysis focusing on assessment-guided LSTM variants.
fig_fair_lstm_vs_gru.png
Matched LSTM vs GRU comparison.
fig_threshold_sensitivity.png
Threshold sensitivity analysis for low-DO risk interpretation.


7. Data and split files
Raw data
data/raw/iot_data.csv
Processed data
data/processed/processed_iot_dataset_with_assessment.csv
data/processed/processed_iot_dataset_with_features.csv
Split metadata
splits/fold0_test_timestamps.csv
splits/fold1_test_timestamps.csv
splits/fold2_test_timestamps.csv
splits/sequence_split_summary_all_folds.csv
These files preserve the split logic used for the reported experiments.


8. Reproducibility notes
Important clarification
This repository reflects two stages of revision:
Stage 1 — Major revision legacy package
The first revised package generated:
the legacy benchmark outputs,
the assessment-guided LSTM analyses,
rolling-origin validation,
uncertainty outputs,
and the main decision-support diagnostics.
Stage 2 — Second review update
The second review required comparison with a stronger recent model.
For this reason:
PatchTST-lite was added,
but the legacy pipeline was kept frozen,
meaning the original models were not rerun solely because PatchTST was added.
This design was chosen to preserve comparability with the already revised manuscript and to avoid introducing unnecessary protocol drift.


9. How to use
To inspect the main manuscript pipeline
Open:
notebooks/Revised_Design_Experiment.ipynb
To inspect the second-review PatchTST addition
Open:
notebooks/PatchTST_Only_Frozen_Protocol_IJIES.ipynb
To trace manuscript tables and claims
Use:
results/legacy/ for the original revised experiment outputs
results/patchtst_lite/ for the added PatchTST-lite comparator


10. Interpretation guidance
This repository is consistent with the final revised manuscript interpretation:
Raw LSTM is strongest for pure point-forecast RMSE.
PatchTST-lite is a competitive contemporary comparator and is particularly relevant for timestamp-level low-DO sensitivity.
Full Assessment-Guided LSTM remains the main integrative decision-oriented model because it embeds temporal assessment context into forecasting and DSS interpretation.
The paper’s contribution is therefore not that one single model dominates every metric, but that the proposed AIoT framework connects:
assessment context,
forecasting, and
operational decision support.


11. Contact / provenance
Prepared as a reproducibility package for:
IJIES / INASS submission no. 20265445
Primary corresponding repository maintainer:
Firda Amalia
If this package is used in academic work, please cite the corresponding final manuscript version.
