# 📈 Time Series Forecasting: SARIMA vs Random Forest

This project focuses on forecasting **monthly sales data** using both **classical time series models (SARIMA)** and **machine learning approaches (Random Forest)**.
The objective is to evaluate how each method captures seasonality, trend, and short-term dynamics, both **in-sample** and **out-of-sample**, and to compare their performance across different forecast horizons.

---

## 📊 Dataset

- Frequency: **Monthly**
- Target variable: **Sales**
- Strong **annual seasonality (12-month cycle)**
- Moderate trend and volatility

---

## 1️⃣ Exploratory Time Series Analysis

Initial analysis included:

- Time series visualization
- Stationarity assessment
- Autocorrelation analysis using **ACF** and **PACF**

### Key Findings

- The original series is **not stationary**
- Strong seasonal autocorrelation at **lag = 12**
- No significant short-term AR or MA components detected

---

## 2️⃣ SARIMA Modeling

### Model Selection

Based on ACF and PACF analysis:

- Short-term AR and MA components were not required
- Seasonality is the dominant structure in the data

Final SARIMA configuration:

SARIMA (p, d, q) × (P, D, Q, s)
SARIMA (0, 0, 0) × (0, 1, 0, 12)


- `D = 1` and `s = 12` capture annual seasonality
- No manual seasonal decomposition was applied
- Seasonality was modeled directly within the SARIMA structure

---

## 3️⃣ SARIMA – In-Sample Evaluation

The SARIMA model was evaluated on the test set using the following metrics:

- MAE:  **1109.72**
- RMSE: **1248.00**
- AIC: **954.252**
- MAPE: **0.29**

Forecasts successfully capture seasonal peaks and troughs.
Prediction uncertainty was visualized using **95% confidence intervals** obtained via the `get_forecast()` method.

---

## 4️⃣ Machine Learning Approach: Random Forest

To complement SARIMA, a **Random Forest Regressor** was implemented using a **supervised learning formulation** of the time series.

### Feature Engineering

#### Lag Features

- `lag_1` … `lag_12`

Baseline Random Forest performance:

- **MAPE:** `0.15`

This confirms that lagged values alone provide a reasonable short-term forecasting baseline.

---

### Trend & Smoothing Features

To improve performance, additional features were introduced.

#### Moving Averages (MA)

- 2-month MA
- 3-month MA
- 12-month MA

#### Exponentially Weighted Moving Averages (EWMA)

- Half-life = 2
- Half-life = 3
- Half-life = 12

After adding MA and EWMA features:

- **MAPE improved to:** `0.13`

These features help the model capture trend dynamics and reduce short-term noise.

---

### Feature Importance Analysis

Feature importance analysis revealed that:

- **`lag_12`** is the most influential feature

To validate this, `lag_12` was removed from the feature set:

- **MAPE increased by approximately 4%**

This confirms that **annual seasonality is a dominant and essential signal** in monthly sales forecasting.

---

### Random Forest Performance Summary

| Feature Set | MAPE |
|------------|------|
| Lag features only | **0.15** |
| Lag + MA + EWMA | **0.13** |
| Lag + MA + EWMA (without `lag_12`) | **↑ +4%** |

---

## 5️⃣ Out-of-Sample Forecasting (Multi-Horizon Evaluation)

Both SARIMA and Random Forest models were evaluated **out-of-sample** using a rolling / expanding window strategy.

- Forecast horizons range from **1 month** to the full test set length
- Models were retrained for each horizon
- **MAPE** was computed separately for each forecast horizon

### Key Observations

- **Random Forest**
  - Strong performance for short-term forecasts
  - Error increases rapidly as horizon grows

- **SARIMA**
  - Slightly weaker at very short horizons
  - Significantly more stable for medium- and long-term forecasts
  - Explicit seasonal modeling provides robustness as horizon increases

A combined visualization compares **MAPE vs forecast horizon** for both models.

---

## 6️⃣ Conclusions

This study demonstrates that:

- Seasonal differencing alone can be sufficient for highly seasonal time series
- Feature engineering plays a critical role in ML-based forecasting
- `lag_12` is essential for monthly seasonal data
- **Random Forest excels at short-term prediction**
- **SARIMA provides superior stability for longer forecast horizons**

---

## 7️⃣ Future Work

- SARIMAX with external regressors
- Gradient Boosting models (XGBoost / LightGBM)
- Direct vs recursive multi-step forecasting strategies
- Probabilistic forecasting and fan-chart analysis

---

## 📁 Repository Notes

- All experiments are fully reproducible
- Visualizations are included in the notebook
- Model parameters and performance metrics are documented here
