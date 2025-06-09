# 🐍 Python Basics for Data Analytics

## Table of Contents
1. [Introduction to Python for Data Analysis](#introduction-to-python-for-data-analysis)
2. [Working with NumPy](#working-with-numpy)
3. [Pandas DataFrames](#pandas-dataframes)
4. [Data Selection and Filtering](#data-selection-and-filtering)
5. [Data Aggregation and Grouping](#data-aggregation-and-grouping)
6. [Handling Missing Data](#handling-missing-data)
7. [Practice Exercises](#practice-exercises-)
8. [Next Steps](#next-steps)

## Introduction to Python for Data Analysis

Python is the go-to language for data analysis due to its simplicity and powerful libraries. In this module, we'll focus on the essential Python concepts and libraries used in data analysis.

### Python Data Types Review

```python
# Numeric types
price = 2500.50      # float
volume = 1000000     # int

# Text type
stock_name = "RELIANCE"  # str

# Boolean
is_rising = True     # bool

# Sequence types
prices = [2500, 2520, 2515, 2530]  # list
prices_tuple = (2500, 2520)         # tuple

# Mapping type
stock_data = {'ticker': 'RELIANCE.NS', 'price': 2500.50}  # dict

# Set
unique_prices = {2500, 2520, 2500, 2515}  # {2500, 2515, 2520}
```

### Control Flow

```python
# If-elif-else
price = 2500
if price > 2500:
    print("Price is above 2500")
elif price == 2500:
    print("Price is exactly 2500")
else:
    print("Price is below 2500")

# For loop
prices = [2500, 2520, 2515, 2530]
for price in prices:
    print(f"Price: {price}")

# While loop
count = 0
while count < 3:
    print(f"Count: {count}")
    count += 1
```

### List Comprehensions

```python
# Basic list comprehension
prices = [2500, 2520, 2515, 2530]
prices_increased = [price * 1.1 for price in prices]  # 10% increase

# With condition
high_prices = [price for price in prices if price > 2510]
```

## Working with NumPy

NumPy is the fundamental package for numerical computing in Python. It provides support for large, multi-dimensional arrays and matrices.

### Creating Arrays

```python
import numpy as np

# Create arrays
prices = np.array([2500, 2520, 2515, 2530, 2550])
volumes = np.array([1000000, 1200000, 950000, 1100000, 1300000])

# Array attributes
print(f"Shape: {prices.shape}")  # (5,)
print(f"Data type: {prices.dtype}")  # int64
print(f"Number of dimensions: {prices.ndim}")  # 1
```

### Array Operations

```python
# Basic operations
price_change = np.diff(prices)  # [20, -5, 15, 20]
percent_change = np.diff(prices) / prices[:-1] * 100

# Statistical operations
mean_price = np.mean(prices)
max_price = np.max(prices)
min_price = np.min(prices)
std_dev = np.std(prices)

# Element-wise operations
total_value = prices * volumes  # Element-wise multiplication
```

### Indexing and Slicing

```python
# Indexing
first_price = prices[0]      # 2500
last_price = prices[-1]       # 2550

# Slicing
first_three = prices[:3]     # [2500, 2520, 2515]
every_other = prices[::2]    # [2500, 2515, 2550]

# Boolean indexing
high_prices = prices[prices > 2520]  # [2530, 2550]
```

## Pandas DataFrames

Pandas is built on top of NumPy and provides high-performance, easy-to-use data structures and data analysis tools.

### Creating DataFrames

```python
import pandas as pd

# From dictionary
data = {
    'Date': ['2023-01-01', '2023-01-02', '2023-01-03'],
    'Open': [2500, 2510, 2505],
    'High': [2520, 2530, 2525],
    'Low': [2490, 2500, 2495],
    'Close': [2510, 2525, 2515],
    'Volume': [1000000, 1200000, 950000]
}

df = pd.DataFrame(data)

# Set Date as index
df['Date'] = pd.to_datetime(df['Date'])
df.set_index('Date', inplace=True)

# Display first few rows
print(df.head())

# Basic information
print(df.info())
print(df.describe())
```

### Reading Data

```python
# From CSV
df = pd.read_csv('stock_data.csv')

# From Excel
df = pd.read_excel('stock_data.xlsx')

# From URL
url = 'https://query1.finance.yahoo.com/v7/finance/download/RELIANCE.NS?period1=1609459200&period2=1640995200&interval=1d&events=history'
df = pd.read_csv(url)
```

## Data Selection and Filtering

### Selecting Data

```python
# Select a single column
close_prices = df['Close']

# Select multiple columns
price_data = df[['Open', 'High', 'Low', 'Close']]

# Select rows by index
first_day = df.iloc[0]          # First row
last_day = df.iloc[-1]           # Last row
first_three_days = df.iloc[:3]   # First three rows

# Select by label
specific_day = df.loc['2023-01-02']

# Boolean indexing
high_volume = df[df['Volume'] > 1000000]
up_days = df[df['Close'] > df['Open']]
```

### Adding and Modifying Columns

```python
# Add a new column
df['Daily_Return'] = df['Close'].pct_change() * 100

# Add a moving average
df['MA_7'] = df['Close'].rolling(window=7).mean()

# Apply a function
df['Price_Change'] = df['Close'].diff()
df['Direction'] = df['Price_Change'].apply(lambda x: 'Up' if x > 0 else 'Down' if x < 0 else 'Unchanged')
```

## Data Aggregation and Grouping

### Basic Aggregations

```python
# Basic statistics
mean_close = df['Close'].mean()
max_volume = df['Volume'].max()

# Multiple aggregations
stats = df.agg({
    'Open': ['mean', 'min', 'max'],
    'Volume': ['sum', 'mean', 'std']
})
```

### Grouping Data

```python
# Add a month column for grouping
df['Month'] = df.index.month_name()

# Group by month
monthly_stats = df.groupby('Month').agg({
    'Close': ['mean', 'min', 'max'],
    'Volume': 'sum'
})

# Pivot tables
pivot = pd.pivot_table(df, values='Close', index='Month', 
                      aggfunc=['mean', 'min', 'max'])
```

## Handling Missing Data

### Detecting Missing Data

```python
# Check for missing values
print(df.isnull().sum())

# Visualize missing data
import seaborn as sns
sns.heatmap(df.isnull(), cbar=False)
```

### Dealing with Missing Data

```python
# Drop rows with any missing values
df_cleaned = df.dropna()

# Fill missing values
df_filled = df.fillna(method='ffill')  # Forward fill
df_filled = df.fillna(method='bfill')  # Backward fill
df_filled = df.fillna(df.mean())      # Fill with mean

# Interpolate missing values
df_interpolated = df.interpolate()
```

## Practice Exercises 📝

1. **Stock Data Analysis**
   - Download stock data for any Indian company for the last 3 months
   - Calculate the 7-day and 21-day moving averages
   - Create a new column 'Signal' that is 'Buy' when 7-day MA crosses above 21-day MA and 'Sell' when it crosses below
   - Calculate the returns if you followed this strategy

2. **Volatility Analysis**
   - Calculate daily returns for a stock
   - Compute the rolling 20-day standard deviation of returns (historical volatility)
   - Identify periods of high volatility (above 2 standard deviations from the mean)
   - Visualize the price series with high volatility periods highlighted

3. **Sector Analysis**
   - Create a DataFrame with at least 5 stocks from different sectors
   - Calculate daily returns for each stock
   - Compute the correlation matrix of returns
   - Visualize the correlation matrix using a heatmap
   - Identify which stocks move together and which don't

## Next Steps

In the next module, we'll explore various data collection techniques, including web scraping and API usage, to gather financial data for analysis.

🔙 [Previous: Getting Started](../1-getting-started/README.md) | 
🔜 [Next: Data Collection →](../3-data-collection/README.md)
