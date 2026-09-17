# Experiment Redesign Summary — IJIES Submission No. 20265445

This document maps every change in the revised experiment notebook
(`Revised_Design_Experiment.ipynb`) to a specific reviewer comment, so it can
be used directly as supporting evidence for the Response Letter and as a
changelog for the manuscript revision. Full numerical outputs are provided
in `revised_results/`.

---

## Critical bug found and fixed (affects all ablation numbers)

**What was wrong:** `TEMPORAL_FEATURES` (the "raw sensor" feature set used
by `Raw_LSTM`, `Raw_GRU`, and both new SOTA-style baselines) was built using
the filter `c.split('_')[0] in ['temp','ph','do']`. This filter also
matched columns such as `temp_score_roll_mean4` and
`do_excursion_flag_roll_mean8`, because the first token of those column
names also happens to be `'temp'`/`'do'` — even though they are clearly
suitability/excursion features, not raw sensor features. As a result, **15
assessment-derived features leaked into what was supposed to be the "raw",
assessment-free baseline**.

**Evidence from the first full run:** `Raw_LSTM` was recorded with 73
features (it should have 58), and on event-based recall and AUC, `Raw_LSTM`
actually outperformed `Full_Assessment_Guided_LSTM` — an anomaly consistent
with the hypothesis that Raw was quietly receiving part of the assessment
signal for free.

**This bug pre-dates the redesign work** — it already existed in the
earliest version of the experiment notebook and was inherited without being
re-audited during the redesign, which was an oversight in that process.

**What was NOT affected:** the no-future-leakage audit remains valid (a
different class of issue), and the `Raw_LSTM` vs. `Raw_GRU` comparison
remains fair (both were equally contaminated, so the conclusion "no
significant LSTM vs. GRU difference" still holds).

**Fix:** `TEMPORAL_FEATURES` is now built explicitly from the column names
actually generated in the lag/rolling loop, rather than guessed via string
matching, making it impossible to mis-capture assessment columns. Corrected
feature-set sizes: `Raw_LSTM`/`Raw_GRU` = 58 (was 73), `LSTM_Suitability` =
88, `LSTM_Excursion` = 74, `LSTM_Stability` = 76, `Full_Assessment_*` = 122
(unchanged, since the final union of features is the same either way). All
numbers in `revised_results/` reflect the corrected feature sets.

---

## Reviewer 2 #2 — Unfair GRU baseline comparison (FIXED)

**Before:** `GRU_Baseline` silently used the full feature set
(sensor+temporal+suitability+excursion+stability), while the `Raw_LSTM` it
was compared against used only sensor+temporal. This was confirmed directly
in the original code (`FEATURE_SETS['GRU_Baseline']` = full feature set).

**Now:** `Raw_GRU` (identical inputs to `Raw_LSTM`) and `Full_Assessment_GRU`
(identical inputs to `Full_Assessment_Guided_LSTM`) are separated cleanly.
Comparing `Raw_LSTM` vs. `Raw_GRU`, and `Full_Assessment_Guided_LSTM` vs.
`Full_Assessment_GRU`, now gives a valid architectural comparison. Paired
Wilcoxon tests across 5 seeds show no significant RMSE difference for either
pair (see `significance_tests_fold0_multiseed.csv`).

---

## Reviewer 1 #8 & Reviewer 2 #1 — No 2024+ SOTA baseline (ADDED)

Two new models were reimplemented from architectures already cited in the
manuscript ([13] Ma et al., CNN-GRU; [14] R.G.D. et al., attention-driven
LSTM/GRU; [15] Hao, GRU-N-Beats): `GRU_NBeats_lite` and
`CNN_GRU_Attention`. Both are trained on the same Raw feature set, window,
horizons, splits, and seeds, giving a fully controlled comparison.

**Transparency note:** these are lightweight reimplementations based on the
published architectural descriptions, trained on this study's own dataset —
not copies of the original authors' code (not publicly available). This is
stated explicitly in the manuscript so the results are not mistaken for a
reproduction of the original papers' reported numbers.

---

## Reviewer 2 #4 — 14-day / single-site dataset insufficient (PARTIALLY STRENGTHENED)

Added **rolling-origin walk-forward evaluation** (fold 1 and fold 2) on top
of the original split (fold 0) — each fold is tested on a different,
non-overlapping time block that was not used for training.

**Additional finding (not raised by the reviewer, found during data audit):**
the four ponds were in fact **not observed in parallel**, but sequentially:
Catfish (1–14 Jan) → Gourami (15–28 Jan) → Tilapia (29 Jan–11 Feb) → Nilem
(12–25 Feb). The dataset actually spans roughly 8 calendar weeks, not just
"14 days" as the manuscript could be read to imply. Per the thesis
advisor's guidance, this sequencing detail is treated as internal
experimental logistics and is not narrated in the manuscript text; the
response to Reviewer 2 #4 instead rests on (a) the rolling-origin evaluation
and (b) the four biologically distinct species contexts.

**Limitation still honestly disclosed:** rolling-origin folds 1–2 currently
run a single seed each (not five) due to free-tier Colab compute
constraints — add fold IDs to `MULTISEED_FOLDS` in the training-loop cell to
extend multi-seed repetition if more compute time becomes available.

---

## Reviewer 2 #5 — No uncertainty quantification (ADDED)

Every configuration on fold 0 was retrained across 5 seeds (`42, 7, 123,
2024, 99`). Outputs: `rmse_uncertainty_fold0_multiseed.csv`,
`recall_uncertainty_fold0_multiseed.csv` (mean, SD, 95% CI), and
`significance_tests_fold0_multiseed.csv` (paired Wilcoxon signed-rank tests
per horizon for the key model comparisons, including the now-fair LSTM vs.
GRU comparison).

**For the manuscript:** single-run figures (e.g. "RMSE 0.3209 vs. 0.3278")
are replaced with "RMSE = 0.32 ± 0.01 (mean ± SD, n = 5 seeds)" format, and
claims such as "stability gives the strongest numerical contribution" are
reported together with their Wilcoxon significance result.

---

## Reviewer 2 #6 — Class prevalence & event-based detection (ADDED)

- `assessment_excursion_do_summary_with_prevalence.csv`: number of low-DO
  timestamps and prevalence percentage per pond.
- `event_based_metrics_primary_run.csv`: number of actual low-DO episodes,
  number detected (event-level recall), and false-alarm episodes —
  complementing the existing timestamp-level metrics.

**For the manuscript:** a new table in Section 4.3 compares timestamp-level
recall with event-level recall, clarifying how many independent low-DO
*episodes* were actually caught, as opposed to how many individual
timestamps.

---

## Reviewer 2 #3 — Suitability/label circularity concern (ADDRESSED BOTH WAYS)

1. **Future-leakage check:** an automated audit
   (`leakage_audit_result.json`) recomputes 30 feature columns directly from
   the raw data — result: PASS, 0 mismatches out of 800 checks. This
   confirms no feature uses information from the future.
2. **Circularity with the label definition:** this is a separate concern —
   `do_score`/`do_gap_ops` use the same operational threshold as the low-DO
   label definition. Addressed with:
   - `threshold_sensitivity_primary_run.csv`: recall recomputed with the
     threshold scaled ±20%. The Suitability configuration's recall
     advantage is small and not fully robust across this range — reported
     honestly rather than only where it looks favorable.
   - `threshold_free_auc_primary_run.csv`: ROC-AUC/PR-AUC of the continuous
     predicted DO value against the low-DO label, independent of any single
     threshold choice.

---

## Reviewer 2 #7 — Feature/hyperparameter reproducibility (ADDED)

- `feature_engineering_config.json`: explicit formula for every engineered
  feature (suitability score, excursion duration, stability CV, etc.).
- `hyperparameter_search_log.csv`: grid `units ∈ {32,64,96}`,
  `dropout ∈ {0.1,0.2,0.3}`, selected by lowest validation RMSE on fold 0.
- `reproducibility_manifest.json`: seeds, fold definitions, final
  architecture, and every feature-set size in one reference file.

---

## Reviewer 1 #6 — Figure quality (FIXED)

All figure titles/labels were converted to English (some were previously in
Indonesian, e.g. the original Figure 3 title "Perbandingan RMSE Antar
Model"). Font sizes were increased, legends moved outside the plot area,
and resolution raised to 220 dpi.

---

## Reviewer 1 #9 — GitHub repository (this repository)

This repository contains the complete implementation
(`Revised_Design_Experiment.ipynb`), the raw dataset (`iot_data.csv`), and
the full results package (`revised_results/`), including
`feature_engineering_config.json`, `reproducibility_manifest.json`,
`environment_info.json`, and `revised_results/README.md`.

---

## Minor corrections (not reviewer-raised, found during this work)

- The number of valid sequences per pond is **~1,293**, not 1,317 as
  previously stated (1,317 did not account for the NaN warm-up period
  created by the 24-step rolling-window features).
- Pond names are now kept consistently in English (Catfish / Gourami /
  Nilem / Tilapia) throughout the code, removing an earlier
  Indonesian-language mapping step that risked mislabeling in manuscript
  tables.

---

## Status

All items above are reflected in the manuscript revision and the
point-by-point Response Letter submitted alongside this repository. The
full run was executed with `QUICK_TEST_MODE = False`, 5 seeds, and all 3
evaluation folds (see `reproducibility_manifest.json`).
