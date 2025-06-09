# 🔍 Exploratory Data Analysis (EDA)

## Table of Contents
1. [Introduction to EDA](#introduction-to-eda)
2. [Univariate Analysis](#univariate-analysis)
3. [Bivariate Analysis](#bivariate-analysis)
4. [Multivariate Analysis](#multivariate-analysis)
5. [Time Series Analysis](#time-series-analysis)
6. [Correlation Analysis](#correlation-analysis)
7. [Statistical Testing](#statistical-testing)
8. [Practice Exercises](#practice-exercises-)
9. [Next Steps](#next-steps)

## Introduction to EDA

Exploratory Data Analysis (EDA) is the process of analyzing and visualizing data to understand its main characteristics, discover patterns, and generate hypotheses. In financial analysis, EDA helps us understand market behavior, identify trends, and detect anomalies.

### Objectives of EDA

1. **Understand the data**: Data types, distributions, and basic statistics
2. **Detect anomalies**: Outliers, missing values, and inconsistencies
3. **Identify patterns**: Trends, seasonality, and relationships
4. **Test assumptions**: Check statistical assumptions for further analysis
5. **Generate hypotheses**: Formulate questions for deeper analysis

### Example Dataset

Let's use the NIFTY 50 index and its constituents for our EDA:

```python
import yfinance as yf
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# Set style for better visualizations
plt.style.use('seaborn')
sns.set_palette("viridis")

# Download NIFTY 50 data
nifty_tickers = ['^NSEI', 'RELIANCE.NS', 'TCS.NS', 'HDFCBANK.NS', 'INFY.NS', 'HINDUNILVR.NS',
                 'ICICIBANK.NS', 'KOTAKBANK.NS', 'HDFC.NS', 'ITC.NS', 'BHARTIARTL.NS']

data = yf.download(nifty_tickers, start='2020-01-01', end='2023-12-31')['Close']

# Calculate daily returns
returns = data.pct_change().dropna()

print("First 5 rows of the dataset:")
print(data.head())
print("\nDataset shape:", data.shape)
```

## Univariate Analysis

Univariate analysis focuses on analyzing a single variable at a time.

### 1. Descriptive Statistics

```python
# Basic statistics
print("Descriptive Statistics:")
print(returns.describe().T)

# Skewness and Kurtosis
print("\nSkewness and Kurtosis:")
skew_kurt = pd.DataFrame({
    'Skewness': returns.skew(),
    'Kurtosis': returns.kurtosis()
})
print(skew_kurt)
```

### 2. Distribution Plots

```python
# Histogram and KDE for NIFTY 50 returns
plt.figure(figsize=(12, 6))
sns.histplot(returns['^NSEI'], kde=True, bins=50)
plt.title('Distribution of NIFTY 50 Daily Returns')
plt.xlabel('Daily Return')
plt.ylabel('Frequency')
plt.axvline(returns['^NSEI'].mean(), color='r', linestyle='--', label='Mean')
plt.axvline(returns['^NSEI'].median(), color='g', linestyle='-', label='Median')
plt.legend()
plt.show()

# Box plot for all stocks
plt.figure(figsize=(12, 6))
returns.boxplot()
plt.title('Box Plot of Daily Returns')
plt.xticks(rotation=45)
plt.ylabel('Daily Return')
plt.tight_layout()
plt.show()
```

### 3. Time Series Plot

```python
# Plot cumulative returns
cumulative_returns = (1 + returns).cumprod()

plt.figure(figsize=(14, 7))
for column in cumulative_returns.columns:
    plt.plot(cumulative_returns.index, cumulative_returns[column], label=column)

plt.title('Cumulative Returns of NIFTY 50 and Constituents')
plt.xlabel('Date')
plt.ylabel('Cumulative Return')
plt.legend(bbox_to_anchor=(1.05, 1), loc='upper left')
plt.grid(True)
plt.tight_layout()
plt.show()
```

## Bivariate Analysis

Bivariate analysis examines the relationship between two variables.

### 1. Scatter Plots

```python
# Scatter plot of two stocks (e.g., RELIANCE vs TCS)
plt.figure(figsize=(10, 6))
sns.scatterplot(x=returns['RELIANCE.NS'], y=returns['TCS.NS'], alpha=0.6)
plt.title('Daily Returns: RELIANCE vs TCS')
plt.xlabel('RELIANCE Returns')
plt.ylabel('TCS Returns')

# Add regression line
sns.regplot(x=returns['RELIANCE.NS'], y=returns['TCS.NS'], scatter=False, color='red')
plt.grid(True)
plt.show()
```

### 2. Pair Plots

```python
# Pair plot for selected stocks
selected_stocks = ['^NSEI', 'RELIANCE.NS', 'HDFCBANK.NS', 'INFY.NS']
sns.pairplot(returns[selected_stocks].dropna(), kind='reg', diag_kind='kde')
plt.suptitle('Pair Plot of Selected Stocks', y=1.02)
plt.show()
```

## Multivariate Analysis

Multivariate analysis examines multiple variables simultaneously.

### 1. Correlation Matrix

```python
# Calculate correlation matrix
corr_matrix = returns.corr()

# Plot heatmap
plt.figure(figsize=(12, 10))
sns.heatmap(corr_matrix, annot=True, cmap='coolwarm', center=0, fmt='.2f',
            linewidths=0.5, cbar_kws={'label': 'Correlation Coefficient'})
plt.title('Correlation Matrix of Daily Returns')
plt.xticks(rotation=45)
plt.yticks(rotation=0)
plt.tight_layout()
plt.show()
```

### 2. Clustermap

```python
# Clustered heatmap
sns.clustermap(corr_matrix, cmap='coolwarm', center=0, figsize=(12, 10),
               row_cluster=True, col_cluster=True, annot=True, fmt='.2f')
plt.suptitle('Clustered Correlation Matrix', y=1.02)
plt.show()
```

## Time Series Analysis

### 1. Decomposition

```python
from statsmodels.tsa.seasonal import seasonal_decompose

# Decompose NIFTY 50 returns
result = seasonal_decompose(returns['^NSEI'].dropna(), period=22)  # 22 trading days in a month

# Plot decomposition
plt.figure(figsize=(12, 10))
result.plot()
plt.suptitle('Time Series Decomposition of NIFTY 50 Returns', y=1.02)
plt.tight_layout()
plt.show()
```

### 2. Rolling Statistics

```python
# Calculate rolling statistics
rolling_window = 22  # 1 month of trading days

# Rolling mean and standard deviation
rolling_mean = returns['^NSEI'].rolling(window=rolling_window).mean()
rolling_std = returns['^NSEI'].rolling(window=rolling_window).std()

# Plot rolling statistics
plt.figure(figsize=(14, 7))
plt.plot(returns.index, returns['^NSEI'], label='Daily Returns', alpha=0.5)
plt.plot(rolling_mean.index, rolling_mean, label=f'Rolling Mean ({rolling_window}d)', color='red')
plt.fill_between(rolling_std.index, 
                 rolling_mean - 2*rolling_std, 
                 rolling_mean + 2*rolling_std, 
                 color='red', alpha=0.1, label='Rolling Std (2σ)')
plt.title('NIFTY 50 Daily Returns with Rolling Statistics')
plt.xlabel('Date')
plt.ylabel('Daily Return')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

## Correlation Analysis

### 1. Rolling Correlation

```python
# Calculate 3-month rolling correlation between NIFTY and RELIANCE
rolling_corr = returns['^NSEI'].rolling(window=63).corr(returns['RELIANCE.NS'])

# Plot rolling correlation
plt.figure(figsize=(14, 6))
rolling_corr.plot()
plt.axhline(y=returns[['^NSEI', 'RELIANCE.NS']].corr().iloc[0, 1], 
             color='r', linestyle='--', label='Overall Correlation')
plt.title('3-Month Rolling Correlation: NIFTY 50 vs RELIANCE')
plt.xlabel('Date')
plt.ylabel('Correlation Coefficient')
plt.legend()
plt.grid(True)
plt.tight_layout()
plt.show()
```

### 2. Correlation with Economic Indicators

```python
# Download economic data (example: USD/INR exchange rate)
econ_data = yf.download('INR=X', start='2020-01-01', end='2023-12-31')['Close']

# Calculate correlation with NIFTY
combined = pd.concat([data['^NSEI'], econ_data], axis=1).dropna()
combined.columns = ['NIFTY', 'USD/INR']

# Calculate rolling correlation
rolling_corr_econ = combined['NIFTY'].pct_change().rolling(window=63).corr(combined['USD/INR'].pct_change())

# Plot
plt.figure(figsize=(14, 6))
rolling_corr_econ.plot()
plt.title('3-Month Rolling Correlation: NIFTY 50 vs USD/INR Exchange Rate')
plt.xlabel('Date')
plt.ylabel('Correlation Coefficient')
plt.grid(True)
plt.tight_layout()
plt.show()
```

## Statistical Testing

### 1. Normality Tests

```python
from scipy import stats

# Shapiro-Wilk test for normality
def test_normality(data, alpha=0.05):
    stat, p_value = stats.shapiro(data)
    print(f'Test Statistic: {stat:.4f}, p-value: {p_value:.4f}')
    if p_value > alpha:
        print('Sample looks Gaussian (fail to reject H0)')
    else:
        print('Sample does not look Gaussian (reject H0)')

print("Shapiro-Wilk Test for NIFTY 50 Returns:")
test_normality(returns['^NSEI'].dropna())

# Q-Q plot
plt.figure(figsize=(8, 6))
stats.probplot(returns['^NSEI'].dropna(), dist="norm", plot=plt)
plt.title('Q-Q Plot of NIFTY 50 Returns')
plt.show()
```

### 2. Stationarity Test (ADF Test)

```python
from statsmodels.tsa.stattools import adfuller

def adf_test(series):
    result = adfuller(series, autolag='AIC')
    print(f'ADF Statistic: {result[0]:.4f}')
    print(f'p-value: {result[1]:.4f}')
    print('Critical Values:')
    for key, value in result[4].items():
        print(f'   {key}: {value:.4f}')
    if result[1] <= 0.05:
        print("Strong evidence against the null hypothesis (H0), reject H0. Data is stationary.")
    else:
        print("Weak evidence against the null hypothesis, time series is non-stationary.")

print("\nADF Test for NIFTY 50 Prices:")
adf_test(data['^NSEI'].dropna())

print("\nADF Test for NIFTY 50 Returns:")
adf_test(returns['^NSEI'].dropna())
```

## Practice Exercises 📝

1. **Sector Analysis**
   - Download data for stocks from different sectors (Banking, IT, FMCG, Auto, Pharma)
   - Perform univariate analysis for each sector
   - Compare the performance and volatility across sectors
   - Identify which sectors outperformed/underperformed the market

2. **Event Study**
   - Select a major market event (e.g., COVID-19 crash, budget announcement)
   - Analyze the impact on different stocks/sectors
   - Calculate abnormal returns around the event window
   - Visualize the cumulative abnormal returns

3. **Pairs Trading Strategy**
   - Identify pairs of stocks with high correlation
   - Calculate the spread between their normalized prices
   - Implement a simple mean-reversion strategy
   - Backtest the strategy and calculate performance metrics

## Next Steps

In the next module, we'll dive into Time Series Analysis, where we'll explore more advanced techniques for analyzing and forecasting time series data, including ARIMA, SARIMA, and other time series models.

🔙 [Previous: Data Cleaning](../4-data-cleaning/README.md) | 
🔜 [Next: Time Series Analysis →](../6-time-series-analysis/README.md)
