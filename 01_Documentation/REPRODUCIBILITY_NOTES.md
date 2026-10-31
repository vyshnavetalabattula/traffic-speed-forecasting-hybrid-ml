# Reproducibility and caveats

## What was checked during packaging
- ZIP source members listed and read successfully; final archive CRC integrity checked.
- Notebook source cells reorganized *without editing executable code*; outputs in organized and archival notebooks cleared to avoid presenting stale exceptions as results.
- Model and result files are retained byte-for-byte, with known invalid files moved to clearly marked historical area.
- No training, metric regeneration, clean-kernel notebook Run All, or model load test performed during packaging.

## Before attempting a clean rerun
- Arrange dataset paths to match `/content/metr-la.h5`, `/content/pems-bay-dst-corrected.h5` and model paths `/content/traffic_models/...`, or adjust notebook paths.
- The organized notebook is a stage-by-stage code reference, **not an independently runnable ordered pipeline**. In particular, original notebook code can rely on variables from prior exploratory cells. Avoid `Run all`.
- Packages: numpy, pandas, tables, torch, xgboost, scikit-learn, joblib, shap, matplotlib. Saved versions were not pinned, so output may vary.
- **METR-LA**: known within-training temporal leakage in median imputation for base-vs-fusion split. Results are accurate to the historical saved run, not proof of corrected methodology. Re-evaluation after genuinely strict base-only imputation would require retraining.
- Previous METR-LA `metr_la_test_comparison.csv` was produced with incorrect feature order; quarantined. `metr_la_15min_condition_analysis.csv` and `weighted_fusion_15min_validation.csv` were affected by the same issue and quarantined.
- Original METR-LA history has multiple exploratory architectures/checkpoints; choose models according to corresponding metadata and verify 24-column feature order before inference.
- The DST-corrected PEMS-BAY dataset uses `America/Los_Angeles`; 2017-03-12 spring shift is real, not twelve missing measurement rows.
- Zeros treated as unavailable speed readings for primary evaluation; genuine-zero alternate training was not run. MAPE for true zeros is undefined.
- SHAP on XGBoost only; no explicit spatial graph model.
- Statistical uncertainty results are hourly sampled day-block bootstraps, not external replication.
- CPU timings use synthetic input batches and exclude feature construction / I/O.

## Guidance for professor demo
Open `01_Documentation/EXPERIMENT_SUMMARY.md` and the figures/CSVs, discuss trained model files and methodology, then open selected stages in the organized notebook. **Do not spend hours running all training code live.** You can discuss checkpoints and rerun a small prediction test after adapting paths.
