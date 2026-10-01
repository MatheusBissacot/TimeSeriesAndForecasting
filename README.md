# Time Series and Forecasting — Final Project

Forecasting project analyzing hourly electricity consumption using ARIMA/SARIMA models.

**Authors:** Matheus Hirakawa Bissacot, Bernardo Gil Alves Salgado, Michal Dawid Kowalski

## Dataset

[Hourly Energy Consumption](https://www.kaggle.com/datasets/robikscube/hourly-energy-consumption/data)
(PJM Interconnection, Kaggle): over 10 years of hourly electricity consumption in
Megawatts. The analysis focuses on the **American Electric Power (AEP)** series
(`archive/AEP_hourly.csv`), which has no missing values.

## Notebooks

- **`TSA.ipynb`**: the main analysis notebook — full exploratory data analysis, feature
  engineering, and a single, more deeply diagnosed **SARIMA** model.
- **`TSA_new.ipynb`**: a revised version that repeats the EDA and additionally fits and
  compares an **ARIMA** model against several **SARIMA** configurations, selecting the
  best one by AIC/p-value before running cross-validation on it.

## Analysis workflow

1. **Data visualization & feature extraction**: time series plot, histogram of
   consumption, periodogram analysis (dominant frequencies), and derived time features
   (hour, day of week, month, season) to expose daily/weekly/seasonal patterns.
   - Daily: lowest overnight, peaks in the evening (6–7 PM).
   - Weekly: slightly lower consumption on weekends.
   - Seasonal: highest in winter, more variable but generally elevated in summer.
2. **Seasonal decomposition**: trend, seasonal, and residual components isolated via
   `seasonal_decompose`, revealing a rise-fall-recovery trend pattern (2005–2013+) and
   strong yearly seasonality.
3. **Stationarity check**: Augmented Dickey-Fuller test (p = 0.43) confirms the series is
   non-stationary, motivating differencing for ARIMA/SARIMA.
4. **ACF/PACF analysis**: used to guide the choice of autoregressive/moving-average
   orders.
5. **Train/test split**: training on data before 2016, testing on data from 2016 onward.
6. **Modelling**:
   - `TSA.ipynb`: SARIMA with order `(p,1,q)` and seasonal order `(1,1,1,12)`, validated
     with residual analysis, ACF/PACF of residuals, a Ljung-Box test (p = 0.622, no
     significant autocorrelation left in residuals), and `TimeSeriesSplit`
     cross-validation (mean RMSE ≈ 1080.28 MW).
   - `TSA_new.ipynb`: ARIMA and multiple SARIMA candidates are fit and compared; the
     SARIMA model with the lowest AIC and highest p-value is selected as final and is the
     only one cross-validated.

## Conclusion

The SARIMA model captures the strong yearly seasonality and autoregressive structure of
the AEP consumption series well, with residuals resembling white noise and a
cross-validated RMSE that is reasonable relative to the series' range (~10,000–25,000 MW).
Remaining concerns flagged for future work: elevated residuals in the earliest years
(around 2006) and a residual autocorrelation spike at lag 12 that could be investigated
further.
