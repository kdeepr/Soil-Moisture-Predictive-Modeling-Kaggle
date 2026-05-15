# 🌱 Soil Moisture Predictive Modeling Challenge
### Kaggle Academic Competition | Kuladeep Roy G

![Python](https://img.shields.io/badge/Python-3.12-blue?logo=python)
![LightGBM](https://img.shields.io/badge/LightGBM-Boosting-orange)
![scikit-learn](https://img.shields.io/badge/scikit--learn-ML-F7931E?logo=scikit-learn)
![Kaggle](https://img.shields.io/badge/Kaggle-Competition-20BEFF?logo=kaggle)
![TimeSeries](https://img.shields.io/badge/Time%20Series-Forecasting-green)

---

## 📌 Overview
A machine learning solution for predicting daily surface soil moisture 
levels over a 93-day horizon (Oct–Dec 2024) using historical soil moisture 
and precipitation data. The solution uses a delta-modeling approach with 
an inverse-RMSE weighted ensemble of 5 models and recursive forecasting.

---

## 🎯 Problem Statement
Predict daily surface soil moisture (`sm_surface`) for 93 future days 
given 639 days of historical observations and precipitation data.

| Dataset | Period | Rows |
|---|---|---|
| Training Data | Jan 2023 – Sep 2024 | 639 days |
| Test Data | Oct 2024 – Dec 2024 | 93 days |
| Precipitation Data | Full period | Merged feature |

---

## 💡 Solution Approach

### Delta Modeling Strategy
Instead of predicting absolute soil moisture values, the model predicts 
the **daily change (delta)** in soil moisture — then reconstructs the 
actual value via recursive forecasting. This prevents error compounding 
and stabilizes long-horizon predictions.

Predicted SM(t) = SM(t-1) + Predicted Delta(t)

### Recursive Forecasting Blend
Each prediction is a weighted combination of:

| Component | Weight | Description |
|---|---|---|
| Persistence | 52% | Last observed value |
| Delta Model | 32% | Ensemble delta prediction |
| Damped Trend | 8% | Weighted recent momentum |
| Seasonal Anchor | 6% | Same day-of-year historical mean |
| Recent Mean | 2% | 14-day rolling mean pull |

---

## 🔧 Feature Engineering (72 Features)

| Category | Features |
|---|---|
| Calendar | Day of year, month, weekday, sin/cos cyclical encodings |
| SM Lags | Lagged values at 1, 2, 3, 5, 7, 10, 14, 21, 30, 45, 60 days |
| Momentum | Daily differences at 1, 2, 3, 7, 14, 30 day intervals |
| Rolling Stats | Mean, std, min, max over 3, 7, 14, 30, 60 day windows |
| Z-scores | SM z-score relative to 14 and 30 day windows |
| Precip Lags | Precipitation lags at 0, 1, 2, 3, 5, 7, 10, 14 days |
| Precip Rolling | Sum, mean, max over 3, 7, 14, 30, 60 day windows |
| Interactions | Precipitation × lagged SM interactions |

---

## 🤖 Ensemble Models

| Model | CV RMSE | Ensemble Weight |
|---|---|---|
| Ridge Regression | 0.01819 | 14.13% |
| Random Forest | 0.01200 | 21.42% |
| Extra Trees | 0.01154 | **22.28%** |
| Gradient Boosting | 0.01242 | 20.70% |
| LightGBM | 0.01197 | 21.47% |

Weights computed via **inverse-RMSE weighting** — better CV score = higher weight.

---

## 📊 Validation Strategy
- **Method:** 5-fold TimeSeriesSplit (respects temporal ordering)
- **Metric:** Root Mean Squared Error (RMSE)
- **Best Model:** Extra Trees (CV RMSE = 0.01154)
- **Ensemble CV RMSE:** ~0.01190

---

## 📁 Repository Structure

```
Soil-Moisture-Predictive-Modeling-Kaggle/
├── soil-moisture-challenge-notebook-4.ipynb  # Main solution notebook
├── training_data.csv                          # Historical SM data
├── test_data_X.csv                            # Test period dates
├── precipitation_data.csv                     # Daily precipitation
└── README.md

```
---

## 🚀 How to Run

```bash
pip install pandas numpy scikit-learn lightgbm
jupyter notebook soil-moisture-challenge-notebook-4.ipynb
```

---

## 🛠️ Tech Stack
| Component | Technology |
|---|---|
| Language | Python 3.12 |
| ML Models | scikit-learn, LightGBM |
| Time Series CV | TimeSeriesSplit |
| Data Processing | pandas, numpy |
| Platform | Kaggle Notebooks |
