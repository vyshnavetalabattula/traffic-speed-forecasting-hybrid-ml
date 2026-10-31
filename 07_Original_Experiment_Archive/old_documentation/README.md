# Traffic Forecasting Research Project

Title:
A Lightweight and Explainable Hybrid ML Framework
for Short-Term Traffic Flow Prediction in Smart Cities

## Datasets
- METR-LA: 207 sensors
- PEMS-BAY: 325 sensors

## Forecast Horizons
- 15 minutes
- 30 minutes
- 60 minutes

## Models
- Persistence baseline
- XGBoost
- LSTM
- Hybrid XGBoost-LSTM neural fusion

## Project Structure
Datasets/: Original traffic-speed files
Source_Code/: Original uploaded Colab notebooks
Models/: Saved training checkpoints and metadata
Results/: Experimental CSV files, figures and reports
Documentation/: Research notes and reproducibility information

## How to Run
1. Open the notebook(s) inside Source_Code/ in Google Colab.
2. Install dependencies from requirements.txt.
3. Upload the datasets or change the dataset paths.
4. Check notebook cells for original absolute Colab paths.
5. Run notebook cells in their original dependency order.

Trained models are provided so full training is not
necessarily required for demonstrating existing results.

## Experimental Notes
- Dataset splits are chronological.
- The XGBoost model uses 24 engineered features.
- LSTM uses 12 historical five-minute observations.
- SHAP analysis explains the XGBoost component only.
- The hybrid has compact storage but greater inference
  latency than standalone XGBoost.
- PEMS-BAY timestamps use America/Los_Angeles to
  account for the March 2017 DST transition.
- Original METR-LA training has a known preprocessing
  limitation involving fallback medians from the
  later fusion-training period.
- Zero-speed treatment is an experimental assumption.
- See validation and sensitivity results for details.

## Reproducibility Limitations
The included notebooks are the source code as exported.
Their execution has not been independently repeated
from a clean environment by this packaging script.
Check paths, library versions, and saved configurations
before reproducing the experiment.

The archive is not a claim that all methodological
limitations have been resolved.
