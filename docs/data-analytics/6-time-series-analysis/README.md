# ⏰ Time Series Analysis

## Table of Contents
1. [Introduction to Time Series Analysis](#introduction-to-time-series-analysis)
2. [Time Series Decomposition](#time-series-decomposition)
3. [Stationarity and Differencing](#stationarity-and-differencing)
4. [Autocorrelation and Partial Autocorrelation](#autocorrelation-and-partial-autocorrelation)
5. [ARIMA Models](#arima-models)
6. [SARIMA Models](#sarima-models)
7. [GARCH Models for Volatility](#garch-models-for-volatility)
8. [Prophet for Time Series Forecasting](#prophet-for-time-series-forecasting)
9. [Practice Exercises](#practice-exercises-)
10. [Next Steps](#next-steps)

## Introduction to Time Series Analysis

Time series analysis involves analyzing time-ordered data points to extract meaningful statistics and characteristics. In financial markets, time series analysis is crucial for forecasting prices, understanding market trends, and making investment decisions.

### Key Concepts

- **Trend**: Long-term movement in the data
- **Seasonality**: Regular patterns that repeat over fixed periods
- **Cyclic**: Patterns without fixed periods
- **Noise**: Random variation in the data
- **Stationarity**: Statistical properties constant over time

### Example Dataset

Let's load and visualize stock price data for analysis:

```python
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from statsmodels.tsa.seasonal import seasonal_decompose
from statsmodels.tsa.stattools import adfuller, acf, pacf
from statsmodels.graphics.tsaplots import plot_acf, plot_pacf
from statsmodels.tsa.arima.model import ARIMA
from statsmodels.tsa.statespace.sarimax import SARIMAX
from arch import arch_model
from prophet import Prophet
from sklearn.metrics import mean_squared_error, mean_absolute_error
import warnings
warnings.filterwarnings('ignore')

# Set style
plt.style.use('seaborn')
sns.set_palette("viridis")

# Download NIFTY 50 data
data = yf.download('^NSEI', start='2018-01-01', end='2023-12-31')

# Use adjusted close price
prices = data['Adj Close']

# Plot the price series
plt.figure(figsize=(14, 7))
plt.plot(prices)
plt.title('NIFTY 50 Index (2018-2023)')
plt.xlabel('Date')
plt.ylabel('Price')
plt.grid(True)
plt.show()
```

## Time Series Decomposition

Decomposing a time series helps us understand its underlying components.

### Additive vs Multiplicative Decomposition

```python
# Decompose the time series
result_add = seasonal_decompose(prices, model='additive', period=252)  # 252 trading days in a year
result_mul = seasonal_decompose(prices, model='multiplicative', period=252)

# Plot additive decomposition
plt.figure(figsize=(12, 10))
result_add.plot()
plt.suptitle('Additive Decomposition', y=1.02)
plt.tight_layout()
plt.show()

# Plot multiplicative decomposition
plt.figure(figsize=(12, 10))
result_mul.plot()
plt.suptitle('Multiplicative Decomposition', y=1.02)
plt.tight_layout()
plt.show()
```

## Stationarity and Differencing

### Testing for Stationarity

```python
def test_stationarity(timeseries):
    # Perform Dickey-Fuller test
    print('Results of Dickey-Fuller Test:')
    dftest = adfuller(timeseries, autolag='AIC')
    dfoutput = pd.Series(dftest[0:4], 
                         index=['Test Statistic', 'p-value', '#Lags Used', 'Number of Observations Used'])
    for key, value in dftest[4].items():
        dfoutput[f'Critical Value ({key})'] = value
    print(dfoutput)
    
    # Plot rolling statistics
    rolmean = timeseries.rolling(window=252, center=False).mean()
    rolstd = timeseries.rolling(window=252, center=False).std()
    
    plt.figure(figsize=(12, 6))
    orig = plt.plot(timeseries, color='blue', label='Original')
    mean = plt.plot(rolmean, color='red', label='Rolling Mean')
    std = plt.plot(rolstd, color='black', label='Rolling Std')
    plt.legend(loc='best')
    plt.title('Rolling Mean & Standard Deviation')
    plt.grid(True)
    plt.show()

# Test original series
print("Original Series:")
test_stationarity(prices)

# Apply log transformation
prices_log = np.log(prices)
print("\nLog Transformed Series:")
test_stationarity(prices_log)

# Apply first order differencing
prices_log_diff = prices_log.diff().dropna()
print("\nFirst Order Differenced Log Series:")
test_stationarity(prices_log_diff)
```

## Autocorrelation and Partial Autocorrelation

### ACF and PACF Plots

```python
# Plot ACF and PACF
plt.figure(figsize=(12, 6))

# ACF
plt.subplot(121)
plot_acf(prices_log_diff, lags=40, alpha=0.05, ax=plt.gca())
plt.title('Autocorrelation Function (ACF)')

# PACF
plt.subplot(122)
plot_pacf(prices_log_diff, lags=40, alpha=0.05, method='ols', ax=plt.gca())
plt.title('Partial Autocorrelation Function (PACF)')

plt.tight_layout()
plt.show()
```

## ARIMA Models

### Building an ARIMA Model

```python
# Split data into train and test
train_size = int(len(prices_log) * 0.8)
train, test = prices_log[0:train_size], prices_log[train_size:len(prices_log)]

# Fit ARIMA model
model = ARIMA(train, order=(1,1,1))  # Example order (p,d,q)
model_fit = model.fit()
print(model_fit.summary())

# Plot residuals
residuals = pd.DataFrame(model_fit.resid)
plt.figure(figsize=(12, 6))
plt.plot(residuals)
plt.title('ARIMA Model Residuals')
plt.show()

# Forecast
forecast = model_fit.forecast(steps=len(test))
forecast_series = pd.Series(forecast, index=test.index)

# Plot forecast vs actual
plt.figure(figsize=(14, 7))
plt.plot(train, label='Training')
plt.plot(test, label='Actual')
plt.plot(forecast_series, label='Forecast')
plt.title('ARIMA Model Forecast vs Actual')
plt.legend()
plt.show()

# Calculate metrics
mse = mean_squared_error(test, forecast_series)
rmse = np.sqrt(mse)
mae = mean_absolute_error(test, forecast_series)
print(f'RMSE: {rmse:.4f}')
print(f'MAE: {mae:.4f}')
```

## SARIMA Models

### Seasonal ARIMA Modeling

```python
# Fit SARIMA model
# Note: This may take some time to run
sarima_model = SARIMAX(train, 
                     order=(1, 1, 1), 
                     seasonal_order=(1, 1, 1, 12),  # 12 for monthly seasonality
                     enforce_stationarity=False,
                     enforce_invertibility=False)

sarima_fit = sarima_model.fit(disp=False)
print(sarima_fit.summary())

# Forecast
forecast = sarima_fit.get_forecast(steps=len(test))
forecast_series = forecast.predicted_mean
conf_int = forecast.conf_int()

# Plot forecast
plt.figure(figsize=(14, 7))
plt.plot(train, label='Training')
plt.plot(test, label='Actual')
plt.plot(forecast_series, label='Forecast')
plt.fill_between(conf_int.index,
                conf_int.iloc[:, 0],
                conf_int.iloc[:, 1], 
                color='k', alpha=0.1)
plt.title('SARIMA Model Forecast vs Actual')
plt.legend()
plt.show()

# Calculate metrics
mse = mean_squared_error(test, forecast_series)
rmse = np.sqrt(mse)
mae = mean_absolute_error(test, forecast_series)
print(f'RMSE: {rmse:.4f}')
print(f'MAE: {mae:.4f}')
```

## GARCH Models for Volatility

### Modeling Volatility with GARCH

```python
# Calculate returns
returns = prices.pct_change().dropna()

# Fit GARCH(1,1) model
model = arch_model(returns, vol='Garch', p=1, q=1)
model_fit = model.fit(disp='off')
print(model_fit.summary())

# Plot conditional volatility
plt.figure(figsize=(14, 6))
plt.plot(returns.index, model_fit.conditional_volatility)
plt.title('Conditional Volatility (GARCH(1,1) Model)')
plt.xlabel('Date')
plt.ylabel('Volatility')
plt.grid(True)
plt.show()

# Forecast volatility
forecasts = model_fit.forecast(horizon=5)
print("\nVolatility Forecast:")
print(np.sqrt(forecasts.variance.dropna().iloc[-1,:]))
```

## Prophet for Time Series Forecasting

### Facebook's Prophet Model

```python
# Prepare data for Prophet
prophet_df = pd.DataFrame({
    'ds': prices.index,
    'y': prices.values
})

# Split into train and test
train_size = int(len(prophet_df) * 0.8)
train = prophet_df.iloc[:train_size]
test = prophet_df.iloc[train_size:]

# Initialize and fit model
model = Prophet(
    yearly_seasonality=True,
    weekly_seasonality=True,
    daily_seasonality=False,
    seasonality_mode='multiplicative'
)
model.fit(train)

# Create future dates for forecasting
future = model.make_future_dataframe(periods=len(test))

# Make predictions
forecast = model.predict(future)

# Plot forecast
fig1 = model.plot(forecast)
plt.title('Prophet Forecast')
plt.show()

# Plot components
fig2 = model.plot_components(forecast)
plt.show()

# Calculate metrics
predictions = forecast['yhat'][-len(test):]
mse = mean_squared_error(test['y'], predictions)
rmse = np.sqrt(mse)
mae = mean_absolute_error(test['y'], predictions)
print(f'RMSE: {rmse:.4f}')
print(f'MAE: {mae:.4f}')
```

## Practice Exercises 📝

1. **ARIMA Model Selection**
   - Download stock data for any Indian company
   - Perform stationarity tests and apply necessary transformations
   - Use ACF and PACF plots to identify potential ARIMA model orders
   - Fit multiple ARIMA models and compare their performance using AIC/BIC
   - Select the best model and make forecasts

2. **Volatility Modeling**
   - Download index data (NIFTY 50 or SENSEX)
   - Fit different GARCH models (GARCH, EGARCH, GJR-GARCH)
   - Compare their performance in modeling volatility
   - Forecast volatility for the next 30 days

3. **Prophet for Stock Price Prediction**
   - Select 3 stocks from different sectors
   - Use Prophet to forecast their prices for the next 3 months
   - Compare the forecasts with actual prices (if available)
   - Analyze which stock's price is most predictable using Prophet

## Next Steps

In the next module, we'll explore Machine Learning for Financial Analysis, where we'll apply various machine learning algorithms to predict stock prices, classify market movements, and build trading strategies.

🔙 [Previous: Exploratory Data Analysis](../5-exploratory-data-analysis/README.md) | 
🔜 [Next: Machine Learning for Financial Analysis →](../7-machine-learning/README.md)
