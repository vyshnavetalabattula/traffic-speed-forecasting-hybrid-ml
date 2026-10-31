# Experimental summary (measured values from uploaded project)

**Dataset:** METR-LA (207 sensors) and PEMS-BAY (325 sensors); 5-minute steps, 12 historical steps, 24 engineered features. **Models:** persistence, XGBoost, one-layer 64-unit LSTM, 65→32→1 neural fusion. Test metrics below reflect historical experiment logs; these models were not rerun while packaging.

## Test results — MAE / RMSE / MAPE percent

| Dataset | Horizon | Persistence | XGBoost | LSTM | Hybrid |
|---|---|---|---|---|---|
| METR-LA | 15 min | 3.4901 / 6.3992 / 8.6158 | 3.1624 / 5.7512 / 8.5614 | 3.2660 / 5.9716 / 8.8514 | 3.1761 / 5.7410 / 8.6033 |
| METR-LA | 30 min | 4.1858 / 8.0130 / 10.8433 | 3.8869 / 7.0179 / 11.1546 | 4.0463 / 7.4392 / 11.8933 | 3.8944 / 6.9983 / 11.0591 |
| METR-LA | 60 min | 5.3357 / 10.2752 / 14.4790 | 4.9104 / 8.4909 / 14.7218 | 5.3688 / 9.2927 / 16.4780 | 4.9220 / 8.4509 / 14.5737 |
| PEMS-BAY | 15 min | 1.5918 / 3.3875 / 3.2314 | 1.4610 / 2.9925 / 3.1216 | 1.4966 / 3.0961 / 3.1402 | 1.4478 / 2.9543 / 3.0597 |
| PEMS-BAY | 30 min | 2.1736 / 4.9540 / 4.6438 | 1.9691 / 4.1543 / 4.5244 | 2.0943 / 4.4682 / 4.8110 | 1.9526 / 4.1070 / 4.4984 |
| PEMS-BAY | 60 min | 3.0480 / 7.0037 / 6.8347 | 2.5851 / 5.2797 / 6.2468 | 2.9342 / 6.0124 / 7.1697 | 2.5361 / 5.2124 / 6.1374 |

PEMS-BAY hybrid beats XGBoost on all three metrics at all horizons, with modest improvements; METR-LA hybrid improves RMSE but slightly worsens MAE at all horizons. **No universal superiority claim.**

## Explainability
On both datasets, TreeSHAP on XGBoost identifies current speed (`lag_0`) and recent rolling speed as high-impact features, while daily-cycle encodings gain importance for longer horizons. **This is not an explanation of the full hybrid network.**

## Storage vs latency
Hybrid model files are ~0.886–0.889 MiB each; hybrid CPU prediction latency for 1024 examples on PEMS-BAY: 38.035 / 36.287 / 37.030 ms (15/30/60 min), versus 6.282 / 6.013 / 6.348 ms XGBoost. Timing excludes preprocessing and data load. Hybrid is lightweight in storage, **not faster than XGBoost**.

## PEMS-BAY sensitivity and uncertainty
217 zero readings in test period; adding zero targets hypothetically leaves hybrid ahead on MAE and RMSE, but does not evaluate retrained zero-retaining models. Paired daily-block 2000-resample bootstrap on hourly sampled test origins found MAE advantage 0.01292 [0.01035, 0.01566] for 15min, 0.01310 [0.00903, 0.01701] for 30min, 0.04993 [0.04322, 0.05744] for 60min; 37 days; descriptive due to temporal dependence.

**Research paper PDF is not provided in the uploaded project.**
