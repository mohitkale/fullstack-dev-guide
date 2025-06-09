# 🚀 Getting Started with Data Analytics

## Table of Contents
1. [Introduction to Data Analytics](#introduction-to-data-analytics)
2. [Why Python for Data Analytics?](#why-python-for-data-analytics)
3. [Setting Up Your Environment](#setting-up-your-environment)
4. [Essential Python Libraries](#essential-python-libraries)
5. [Basic Data Types and Structures](#basic-data-types-and-structures)
6. [Your First Data Analysis](#your-first-data-analysis)
7. [Practice Exercises](#practice-exercises-)
8. [Next Steps](#next-steps)

## Introduction to Data Analytics

Data Analytics is the process of examining, cleaning, transforming, and modeling data to discover useful information, draw conclusions, and support decision-making. In the context of stock market analysis, it helps us understand market trends, make predictions, and develop trading strategies.

### Types of Data Analytics

1. **Descriptive Analytics**: What happened? (e.g., stock price movements last month)
2. **Diagnostic Analytics**: Why did it happen? (e.g., reasons for a stock's price drop)
3. **Predictive Analytics**: What is likely to happen? (e.g., future stock prices)
4. **Prescriptive Analytics**: What should we do? (e.g., buy/sell recommendations)

## Why Python for Data Analytics?

Python is the most popular language for data analytics because:
- Easy to learn and read
- Rich ecosystem of libraries (Pandas, NumPy, Matplotlib, etc.)
- Strong community support
- Excellent for data manipulation and analysis
- Great for both small and large datasets
- Free and open-source

## Setting Up Your Environment

### 1. Install Python
Download and install the latest version of Python from [python.org](https://www.python.org/downloads/).

### 2. Install Jupyter Notebook
Jupyter Notebook is an interactive environment that's great for data analysis:

```bash
pip install notebook
```

### 3. Create a Virtual Environment (Recommended)

```bash
# Create a new virtual environment
python -m venv venv

# Activate the environment
# On Windows:
venv\Scripts\activate
# On macOS/Linux:
source venv/bin/activate
```

### 4. Install Essential Libraries

```bash
pip install numpy pandas matplotlib seaborn jupyter yfinance
```

## Essential Python Libraries

| Library | Purpose |
|---------|---------|
| **NumPy** | Numerical computing with support for large, multi-dimensional arrays |
| **Pandas** | Data manipulation and analysis |
| **Matplotlib** | Basic data visualization |
| **Seaborn** | Statistical data visualization |
| **yfinance** | Download market data from Yahoo Finance |
| **scikit-learn** | Machine learning algorithms |
| **statsmodels** | Statistical modeling |

## Basic Data Types and Structures

### 1. Basic Data Types

```python
# Integer
price = 2500

# Float
volume = 12345.67

# String
ticker = "RELIANCE.NS"

# Boolean
is_rising = True
```

### 2. Data Structures

#### Lists
```python
# List of stock prices
prices = [2500, 2520, 2515, 2530, 2550]

# Accessing elements
first_price = prices[0]  # 2500
last_price = prices[-1]   # 2550

# Slicing
first_three = prices[:3]  # [2500, 2520, 2515]
```

#### Dictionaries
```python
# Stock information
stock = {
    'ticker': 'RELIANCE.NS',
    'open': 2500,
    'high': 2560,
    'low': 2490,
    'close': 2550,
    'volume': 5000000
}

# Accessing values
close_price = stock['close']  # 2550
```

## Your First Data Analysis

Let's analyze some stock market data using Python. We'll use the `yfinance` library to download stock data for Reliance Industries.

### Step 1: Install Required Libraries

```bash
pip install yfinance pandas matplotlib
```

### Step 2: Download Stock Data

```python
import yfinance as yf
import pandas as pd
import matplotlib.pyplot as plt

# Download Reliance Industries data
reliance = yf.download('RELIANCE.NS', start='2023-01-01', end='2023-12-31')

# Display first 5 rows
print(reliance.head())
```

### Step 3: Basic Data Exploration

```python
# Basic information about the dataset
print(reliance.info())

# Statistical summary
print(reliance.describe())

# Check for missing values
print(reliance.isnull().sum())
```

### Step 4: Simple Visualization

```python
# Plot closing prices
plt.figure(figsize=(12, 6))
plt.plot(reliance['Close'])
plt.title('Reliance Industries Stock Price (2023)')
plt.xlabel('Date')
plt.ylabel('Price (₹)')
plt.grid(True)
plt.show()
```

### Step 5: Calculate Daily Returns

```python
# Calculate daily returns
reliance['Daily_Return'] = reliance['Close'].pct_change() * 100

# Plot daily returns
plt.figure(figsize=(12, 6))
plt.plot(reliance['Daily_Return'])
plt.title('Reliance Industries Daily Returns (2023)')
plt.xlabel('Date')
plt.ylabel('Daily Return (%)')
plt.grid(True)
plt.show()
```

## Practice Exercises 📝

1. **Data Collection**
   - Download stock data for TCS (Tata Consultancy Services) for the year 2023
   - Print the highest and lowest closing prices
   - Calculate the average daily trading volume

2. **Basic Analysis**
   - Calculate the 7-day and 30-day moving averages for the closing price
   - Plot both moving averages along with the closing price
   - Identify any crossover points (where the 7-day crosses the 30-day)

3. **Simple Strategy**
   - Create a simple trading strategy: Buy when the 7-day MA crosses above the 30-day MA, and sell when it crosses below
   - Calculate the returns of this strategy
   - Compare with a simple buy-and-hold strategy

## Next Steps

In the next module, we'll dive deeper into Python programming concepts specifically useful for data analysis, including working with NumPy arrays and Pandas DataFrames.

🔜 [Next: Python Basics for Data Analytics →](../2-python-basics/README.md)
