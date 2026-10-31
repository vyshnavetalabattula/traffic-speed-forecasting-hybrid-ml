# Traffic Speed Forecasting Using Hybrid Machine Learning

## Overview

This project investigates short-term traffic speed forecasting using machine learning and deep learning approaches. It compares XGBoost, LSTM and a hybrid neural-fusion model on two real-world traffic datasets: **METR-LA** and **PEMS-BAY**.

The hybrid architecture combines engineered traffic features with temporal patterns learned by an LSTM. A neural fusion network combines the two branches to produce traffic speed predictions.

The experiments evaluate three forecasting horizons:

- 15 minutes
- 30 minutes
- 60 minutes

The project also examines model interpretability, prediction latency, storage requirements and sensitivity to missing or zero-valued readings.

## Experimental Setup

| Component | Description |
|---|---|
| Datasets | METR-LA (207 sensors), PEMS-BAY (325 sensors) |
| Sampling interval | 5 minutes |
| Historical input | 12 time steps |
| Engineered features | 24 |
| Models | Persistence, XGBoost, LSTM, Hybrid |
| LSTM architecture | One-layer, 64-unit LSTM |
| Neural fusion architecture | 65 → 32 → 1 |
| Forecast horizons | 15, 30 and 60 minutes |

The model predicts **traffic sensor speed**, not traffic volume, congestion classes or traffic signal timings.

The current implementation pools examples across sensors, but each input uses only that sensor's own historical speed values and calendar features. It does not use graph-based spatial modelling.

## Repository Structure

```text
traffic-speed-forecasting-hybrid-ml/
│
├── 01_Documentation/
│   ├── EXPERIMENT_SUMMARY.md
│   ├── REPRODUCIBILITY_NOTES.md
│   ├── requirements.txt
│   └── FILE_MAP.json
│
├── 02_Source_Code/
│   └── Professor_Review_Organized.ipynb
│
├── 03_Models/
│   ├── corrected_full_network/
│   ├── full_network/
│   └── pems_bay/
│
└── 07_Original_Experiment_Archive/
    ├── old_documentation/
    ├── 01_Traffic_Dataset_Exploration.ipynb
    └── Original_Notebook_Outputs_Cleared.ipynb
```

## Experimental Results

The following values are taken from the historical experiment logs documented in `01_Documentation/EXPERIMENT_SUMMARY.md`. They were not regenerated during repository packaging.

Each metric entry is presented as:

**MAE / RMSE / MAPE (%)**

### METR-LA

| Horizon | Persistence | XGBoost | LSTM | Hybrid |
|---|---|---|---|---|
| 15 min | 3.4901 / 6.3992 / 8.6158 | 3.1624 / 5.7512 / 8.5614 | 3.2660 / 5.9716 / 8.8514 | 3.1761 / 5.7410 / 8.6033 |
| 30 min | 4.1858 / 8.0130 / 10.8433 | 3.8869 / 7.0179 / 11.1546 | 4.0463 / 7.4392 / 11.8933 | 3.8944 / 6.9983 / 11.0591 |
| 60 min | 5.3357 / 10.2752 / 14.4790 | 4.9104 / 8.4909 / 14.7218 | 5.3688 / 9.2927 / 16.4780 | 4.9220 / 8.4509 / 14.5737 |

### PEMS-BAY

| Horizon | Persistence | XGBoost | LSTM | Hybrid |
|---|---|---|---|---|
| 15 min | 1.5918 / 3.3875 / 3.2314 | 1.4610 / 2.9925 / 3.1216 | 1.4966 / 3.0961 / 3.1402 | 1.4478 / 2.9543 / 3.0597 |
| 30 min | 2.1736 / 4.9540 / 4.6438 | 1.9691 / 4.1543 / 4.5244 | 2.0943 / 4.4682 / 4.8110 | 1.9526 / 4.1070 / 4.4984 |
| 60 min | 3.0480 / 7.0037 / 6.8347 | 2.5851 / 5.2797 / 6.2468 | 2.9342 / 6.0124 / 7.1697 | 2.5361 / 5.2124 / 6.1374 |

### Key Observations

- **PEMS-BAY:** The hybrid model outperforms XGBoost on MAE, RMSE and MAPE across all three forecasting horizons. The improvements are modest.
- **METR-LA:** The hybrid model improves RMSE at all three horizons, but slightly worsens MAE compared with XGBoost at all horizons.
- The hybrid model does not demonstrate universal superiority across datasets and metrics.

These results illustrate why model evaluation should consider multiple datasets and metrics rather than relying on a single performance measure.

## Explainability

TreeSHAP was applied to the XGBoost branch on both datasets.

The analysis identifies current speed (`lag_0`) and recent rolling speed as high-impact features. Daily-cycle encodings become more important at longer forecasting horizons.

**Scope limitation:** These explanations apply to the XGBoost branch only. They are not explanations of the complete hybrid network.

## Storage and Inference Latency

The experiments also examined the trade-off between model size and prediction speed.

| Measurement | Hybrid | XGBoost |
|---|---:|---:|
| Model file size | Approximately 0.886–0.889 MiB per model | — |
| CPU latency, 1024 PEMS-BAY examples, 15 min | 38.035 ms | 6.282 ms |
| CPU latency, 1024 PEMS-BAY examples, 30 min | 36.287 ms | 6.013 ms |
| CPU latency, 1024 PEMS-BAY examples, 60 min | 37.030 ms | 6.348 ms |

Timing excludes preprocessing and data loading.

The hybrid model is lightweight in storage, but its CPU inference is substantially slower than XGBoost. Therefore, the hybrid architecture does not provide a speed advantage.

## PEMS-BAY Sensitivity and Uncertainty Analysis

The PEMS-BAY test period contained 217 zero readings.

The sensitivity analysis examined the hypothetical effect of adding zero-valued targets. Under that analysis, the hybrid model remained ahead on MAE and RMSE. However, this does not evaluate models retrained while retaining genuine zero readings.

A paired daily-block bootstrap with 2,000 resamples was also performed on hourly sampled test origins over 37 days.

The reported MAE advantage of the hybrid model was:

| Horizon | MAE advantage | Bootstrap interval |
|---|---:|---:|
| 15 min | 0.01292 | [0.01035, 0.01566] |
| 30 min | 0.01310 | [0.00903, 0.01701] |
| 60 min | 0.04993 | [0.04322, 0.05744] |

These uncertainty results are descriptive because of temporal dependence.

## Reproducibility and Research Limitations

Please read `01_Documentation/REPRODUCIBILITY_NOTES.md` before attempting to reproduce the experiments.

Important limitations include:

- The repository is an organised archive of existing experiments. Model and result files were retained without retraining or metric regeneration during packaging.
- The organised notebook is a stage-by-stage code reference, not an independently runnable ordered pipeline. Original notebook cells may depend on variables created in earlier exploratory cells. Avoid using `Run All` without first adapting the environment and execution order.
- Notebook paths refer to the original `/content/` environment and may require adjustment.
- A clean end-to-end reproduction was not performed during packaging.
- **METR-LA preprocessing issue:** The historical main experiment has a within-training temporal leakage issue in median imputation for the base-versus-fusion split. Its results reflect the saved historical run, not a verified leakage-free methodology. Correcting this issue would require retraining.
- A previous METR-LA test comparison was generated with an incorrect feature order and was quarantined, along with related affected analyses. These historical results should not be cited.
- PEMS-BAY uses a DST-corrected dataset with the `America/Los_Angeles` timezone. The spring-forward transition is a real time change, not twelve missing measurement rows.
- Zero values were treated as unavailable speed readings in the primary evaluation. A genuine zero-retaining alternate training experiment was not performed.
- MAPE is undefined for true zero targets.
- SHAP explanations cover XGBoost only.
- No explicit spatial graph model was implemented.
- Statistical uncertainty results use hourly sampled, daily-block bootstrap analysis and are not external replication.
- CPU timing uses synthetic input batches and excludes feature construction and I/O.

## Recommended Review Order

1. `01_Documentation/EXPERIMENT_SUMMARY.md` — detailed measured results and analysis.
2. `01_Documentation/REPRODUCIBILITY_NOTES.md` — packaging checks and reproduction caveats.
3. `02_Source_Code/Professor_Review_Organized.ipynb` — organised experimental code.
4. `03_Models/` — saved model checkpoints.
5. `07_Original_Experiment_Archive/` — original notebooks and experiment history.

## Dataset Availability

The raw METR-LA and PEMS-BAY HDF5 datasets, DST-corrected PEMS-BAY copy and separate validated-results artifacts are not included in this public repository.

The documentation reports the historical experiment measurements and explains the associated evaluation caveats.

## Project Status

This repository provides the available documentation, organised experimental source code, saved model checkpoints and original experiment archive.

The project is presented as an experimental comparison of traffic speed forecasting approaches, including measured performance, efficiency trade-offs and known methodological limitations. It does not claim universal model superiority or fully verified end-to-end reproducibility.
