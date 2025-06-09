# 📊 Data Visualization for Financial Analysis

## Table of Contents
1. [Introduction to Financial Visualization](#introduction-to-financial-visualization)
2. [Key Libraries for Visualization](#key-libraries-for-visualization)
3. [Visualizing Time Series Data](#visualizing-time-series-data)
4. [Candlestick Charts for Price Action](#candlestick-charts-for-price-action)
5. [Analyzing Volume and Price Together](#analyzing-volume-and-price-together)
6. [Visualizing Distributions and Relationships](#visualizing-distributions-and-relationships)
7. [Portfolio Visualization](#portfolio-visualization)
8. [Interactive Visualizations](#interactive-visualizations)
9. [Practice Exercises](#practice-exercises-)
10. [Next Steps](#next-steps)

## Introduction to Financial Visualization

Data visualization is a critical skill in financial analysis. It helps in:
- Identifying trends, patterns, and anomalies in financial data.
- Communicating complex information clearly and effectively.
- Supporting data-driven decision-making.
- Monitoring market movements and portfolio performance.

This module will guide you through creating various financial visualizations using Python.

## Key Libraries for Visualization

- **Matplotlib**: The foundational plotting library in Python, offering extensive control over plots.
- **Seaborn**: Built on top of Matplotlib, Seaborn provides a high-level interface for drawing attractive and informative statistical graphics.
- **Plotly**: Creates interactive, publication-quality graphs online and offline. Excellent for web-based dashboards.
- **mplfinance**: Specifically designed for financial data visualization, making it easy to create candlestick charts, OHLC charts, and add technical indicators.

### Setting Up the Environment

```python
import numpy as np
import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt
import seaborn as sns
import plotly.graph_objects as go
import plotly.express as px
import mplfinance as mpf # For financial charts
import warnings
warnings.filterwarnings('ignore')

# Set style
plt.style.use('seaborn-v0_8-whitegrid')
sns.set_palette("viridis")

# Download sample data (e.g., NIFTY 50 Index)
data_viz = yf.download('^NSEI', start='2020-01-01', end='2023-12-31')
# For mplfinance, ensure column names are standard (Open, High, Low, Close, Volume)
data_viz.rename(columns={'Adj Close': 'Adj_Close'}, inplace=True) # mplfinance might prefer standard names
```

## Visualizing Time Series Data

Line charts are commonly used to visualize stock prices over time.

```python
# Basic Line Plot of Closing Prices
plt.figure(figsize=(12, 6))
plt.plot(data_viz.index, data_viz['Close'], label='NIFTY 50 Close Price')
plt.title('NIFTY 50 Closing Prices (2020-2023)')
plt.xlabel('Date')
plt.ylabel('Price (INR)')
plt.legend()
plt.grid(True)
plt.show()

# Line Plot with Moving Averages
data_viz['SMA_20'] = data_viz['Close'].rolling(window=20).mean()
data_viz['SMA_50'] = data_viz['Close'].rolling(window=50).mean()

plt.figure(figsize=(12, 6))
plt.plot(data_viz.index, data_viz['Close'], label='Close Price', alpha=0.7)
plt.plot(data_viz.index, data_viz['SMA_20'], label='20-Day SMA', linestyle='--')
plt.plot(data_viz.index, data_viz['SMA_50'], label='50-Day SMA', linestyle=':')
plt.title('NIFTY 50 with Moving Averages')
plt.xlabel('Date')
plt.ylabel('Price (INR)')
plt.legend()
plt.grid(True)
plt.show()
```

## Candlestick Charts for Price Action

Candlestick charts show the open, high, low, and close (OHLC) prices for a specific period.

```python
# Candlestick chart using mplfinance (last 100 days)
mpf.plot(data_viz.tail(100), 
         type='candle', 
         style='yahoo', # 'charles', 'yahoo', 'sas', etc.
         title='NIFTY 50 Candlestick Chart (Last 100 Days)',
         ylabel='Price (INR)',
         volume=True, 
         mav=(20, 50), # Add 20 and 50-day moving averages
         figratio=(12,7))

# Interactive Candlestick with Plotly
fig_plotly_candle = go.Figure(data=[go.Candlestick(x=data_viz.index,
                                               open=data_viz['Open'],
                                               high=data_viz['High'],
                                               low=data_viz['Low'],
                                               close=data_viz['Close'],
                                               name='NIFTY 50')])

fig_plotly_candle.update_layout(
    title='NIFTY 50 Interactive Candlestick Chart',
    xaxis_title='Date',
    yaxis_title='Price (INR)',
    xaxis_rangeslider_visible=False # Hide range slider for cleaner look
)
fig_plotly_candle.show()
```

## Analyzing Volume and Price Together

Visualizing price and volume on the same chart can reveal insights about market strength.

```python
# Price and Volume chart using mplfinance (already shown in candlestick)
# For a separate volume subplot with Matplotlib:
fig, (ax1, ax2) = plt.subplots(2, 1, figsize=(12, 8), sharex=True, 
                               gridspec_kw={'height_ratios': [3, 1]})

ax1.plot(data_viz.index, data_viz['Close'], label='Close Price')
ax1.plot(data_viz.index, data_viz['SMA_50'], label='50-Day SMA', linestyle='--')
ax1.set_title('NIFTY 50 Price and Volume')
ax1.set_ylabel('Price (INR)')
ax1.legend()
ax1.grid(True)

ax2.bar(data_viz.index, data_viz['Volume'], label='Volume', color='gray')
ax2.set_ylabel('Volume')
ax2.grid(True)

plt.xlabel('Date')
plt.tight_layout()
plt.show()
```

## Visualizing Distributions and Relationships

Histograms, KDE plots, and scatter plots help understand data distributions and correlations.

```python
# Daily Returns
data_viz['Daily_Return'] = data_viz['Close'].pct_change()

# Histogram of Daily Returns
plt.figure(figsize=(10, 6))
sns.histplot(data_viz['Daily_Return'].dropna(), bins=50, kde=True)
plt.title('Distribution of NIFTY 50 Daily Returns')
plt.xlabel('Daily Return')
plt.ylabel('Frequency')
plt.grid(True)
plt.show()

# Correlation Heatmap (using a few tech stocks for example)
tech_tickers = ['TCS.NS', 'INFY.NS', 'WIPRO.NS', 'HCLTECH.NS']
tech_data_list = [yf.download(ticker, start='2020-01-01', end='2023-12-31')['Adj Close'] for ticker in tech_tickers]
tech_returns = pd.concat(tech_data_list, axis=1)
tech_returns.columns = tech_tickers
tech_returns = tech_returns.pct_change().dropna()

plt.figure(figsize=(8, 6))
sns.heatmap(tech_returns.corr(), annot=True, cmap='coolwarm', fmt='.2f')
plt.title('Correlation Matrix of Indian Tech Stock Returns')
plt.show()
```

## Portfolio Visualization

Visualizing portfolio allocation and risk-return profiles.

```python
# Example Portfolio Weights
portfolio_weights = pd.Series({'RELIANCE.NS': 0.3, 'TCS.NS': 0.25, 'HDFCBANK.NS': 0.2, 'INFY.NS': 0.15, 'ITC.NS': 0.1})

# Pie Chart of Portfolio Allocation
plt.figure(figsize=(8, 8))
plt.pie(portfolio_weights, labels=portfolio_weights.index, autopct='%1.1f%%', startangle=140, colors=sns.color_palette('pastel'))
plt.title('Example Portfolio Allocation')
plt.show()

# Risk-Return Scatter Plot (using the tech stocks from above)
risk_return_profile = pd.DataFrame({
    'Mean_Return': tech_returns.mean() * 252, # Annualized
    'Volatility': tech_returns.std() * np.sqrt(252) # Annualized
})

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Volatility', y='Mean_Return', data=risk_return_profile, s=100)
for i, stock in enumerate(risk_return_profile.index):
    plt.text(risk_return_profile['Volatility'][i], risk_return_profile['Mean_Return'][i], stock)
plt.title('Risk-Return Profile of Selected Tech Stocks')
plt.xlabel('Annualized Volatility (Risk)')
plt.ylabel('Annualized Mean Return')
plt.grid(True)
plt.show()
```

## Interactive Visualizations

Plotly is excellent for creating interactive charts that can be embedded in web applications or Jupyter notebooks.

```python
# Interactive Line Chart with Plotly Express
fig_px_line = px.line(data_viz, x=data_viz.index, y=['Close', 'SMA_20', 'SMA_50'],
                      title='NIFTY 50 Interactive Chart with Moving Averages',
                      labels={'value': 'Price (INR)', 'date': 'Date'})
fig_px_line.update_layout(legend_title_text='Legend')
fig_px_line.show()

# Building Dashboards (Conceptual)
# Libraries like Dash or Streamlit can be used to combine multiple Plotly charts
# into interactive web dashboards. This is beyond a single script but an important application.
# Example: A dashboard could show price charts, volume, news sentiment, and portfolio performance.
```

## Practice Exercises 📝

1.  **Custom Candlestick**: Create a candlestick chart for a stock of your choice for the last 6 months. Customize its appearance (colors, style) and add Bollinger Bands as a technical indicator using `mplfinance`.
2.  **Sector Comparison**: Download data for 3-4 stocks from the same sector (e.g., Banking - HDFCBANK.NS, ICICIBANK.NS, SBIN.NS). Plot their normalized closing prices on the same chart to compare their performance over the last year.
3.  **Interactive Scatter Plot**: Create an interactive scatter plot using Plotly showing the risk (annualized volatility) vs. return (annualized mean return) for a basket of 5-7 diverse stocks. Add hover information to show stock names.
4.  **Distribution Analysis**: For a stock of your choice, plot the distribution of its monthly returns. Compare this to a normal distribution curve overlaid on the histogram.
5.  **Volume Profile (Advanced)**: Research and try to implement a basic volume profile visualization for a short period (e.g., one month) of a stock's data. This shows trading volume at different price levels.

## Next Steps

With a solid understanding of data collection, cleaning, EDA, time series analysis, machine learning, and visualization, the next module, **Case Studies**, will integrate these concepts to solve real-world financial analysis problems.

🔙 [Previous: Machine Learning for Financial Analysis](../7-machine-learning/README.md) |
🔜 [Next: Case Studies →](../9-case-studies/README.md)
