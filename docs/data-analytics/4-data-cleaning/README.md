# 🧹 Data Cleaning and Preprocessing

## Table of Contents
1. [Introduction to Data Cleaning](#introduction-to-data-cleaning)
2. [Handling Missing Data](#handling-missing-data)
3. [Dealing with Outliers](#dealing-with-outliers)
4. [Data Transformation](#data-transformation)
5. [Handling Categorical Data](#handling-categorical-data)
6. [Date and Time Data](#date-and-time-data)
7. [Text Data Cleaning](#text-data-cleaning)
8. [Practice Exercises](#practice-exercises-)
9. [Next Steps](#next-steps)

## Introduction to Data Cleaning

Data cleaning is the process of detecting and correcting (or removing) corrupt or inaccurate records from a dataset. In financial analysis, clean data is crucial for accurate modeling and decision-making.

### Common Data Issues

1. **Missing Values**: Empty or NaN values in the dataset
2. **Inconsistent Data**: Inconsistent formats or units
3. **Outliers**: Extreme values that can skew analysis
4. **Duplicate Data**: Repeated entries
5. **Incorrect Data Types**: Numbers stored as strings, dates as strings, etc.
6. **Irrelevant Data**: Data that's not needed for analysis

### Example Dataset

Let's load a sample stock market dataset with some common data quality issues:

```python
import pandas as pd
import numpy as np
import yfinance as yf

# Download sample data
stocks = ['RELIANCE.NS', 'TCS.NS', 'HDFCBANK.NS', 'INFY.NS', 'HINDUNILVR.NS']
data = yf.download(stocks, start='2023-01-01', end='2023-12-31')['Close']

# Introduce some data quality issues for demonstration
np.random.seed(42)

# 1. Add some missing values
mask = np.random.random(data.shape) < 0.02  # 2% missing values
data[mask] = np.nan

# 2. Add some outliers
data.iloc[10, 1] = data.iloc[10, 1] * 1.5  # Single outlier
data.iloc[20:25, 2] = data.iloc[20:25, 2] * 1.3  # Multiple outliers

# 3. Add some inconsistent data (negative prices)
data.iloc[15, 3] = -data.iloc[15, 3]

print("Sample data with introduced issues:")
print(data.head())
print("\nMissing values per column:")
print(data.isnull().sum())
```

## Handling Missing Data

Missing data is a common issue in real-world datasets. Let's explore different strategies to handle it.

### Identifying Missing Data

```python
# Check for missing values
print("Missing values per column:")
print(data.isnull().sum())

# Visualize missing data
import seaborn as sns
import matplotlib.pyplot as plt

plt.figure(figsize=(10, 6))
sns.heatmap(data.isnull(), cbar=False, cmap='viridis')
plt.title("Missing Values Heatmap")
plt.show()
```

### Strategies for Handling Missing Data

1. **Dropping Missing Values**

```python
# Drop rows with any missing values
cleaned_data = data.dropna()
print(f"Original shape: {data.shape}, After dropping missing: {cleaned_data.shape}")

# Drop columns with more than 5% missing values
threshold = len(data) * 0.05
cleaned_data = data.dropna(axis=1, thresh=len(data)-threshold)
print(f"After dropping columns with >5% missing: {cleaned_data.shape}")
```

2. **Filling Missing Values**

```python
# Forward fill (carry forward last valid observation)
data_ffill = data.ffill()

# Backward fill (use next valid observation)
data_bfill = data.bfill()

# Fill with mean/median
mean_fill = data.fillna(data.mean())
median_fill = data.fillna(data.median())

# Interpolation
data_interpolated = data.interpolate(method='linear')

# Compare different methods for one column
plt.figure(figsize=(12, 6))
plt.plot(data['RELIANCE.NS'], 'o', label='Original', alpha=0.3)
plt.plot(data_ffill['RELIANCE.NS'], 'r-', label='Forward Fill')
plt.plot(data_bfill['RELIANCE.NS'], 'g-', label='Backward Fill')
plt.plot(mean_fill['RELIANCE.NS'], 'y-', label='Mean Fill')
plt.plot(data_interpolated['RELIANCE.NS'], 'm-', label='Interpolated')
plt.legend()
plt.title("Comparison of Missing Value Imputation Methods")
plt.show()
```

## Dealing with Outliers

Outliers can significantly affect statistical analyses and machine learning models.

### Detecting Outliers

```python
# Visual detection using box plots
plt.figure(figsize=(12, 6))
data.boxplot()
plt.title("Box Plot of Stock Prices")
plt.xticks(rotation=45)
plt.show()

# Statistical methods
from scipy import stats

# Z-score method
z_scores = np.abs(stats.zscore(data))
outliers_z = np.where(z_scores > 3)  |data| > μ ± 3σ

# IQR method
Q1 = data.quantile(0.25)
Q3 = data.quantile(0.75)
IQR = Q3 - Q1
outliers_iqr = ((data < (Q1 - 1.5 * IQR)) | (data > (Q3 + 1.5 * IQR))).any(axis=1)

print(f"Number of outliers (Z-score > 3): {len(outliers_z[0])}")
print(f"Number of outliers (IQR method): {outliers_iqr.sum()}")
```

### Handling Outliers

```python
# 1. Capping (Winsorization)
def winsorize_series(series, lower=0.05, upper=0.95):
    q_low = series.quantile(lower)
    1q_high = series.quantile(upper)
    return series.clip(lower=q_low, upper=q_high)

winsorized_data = data.apply(winsorize_series)

# 2. Transformation
log_data = np.log1p(data)  # log(1+x) transformation

# 3. Imputation with median (robust to outliers)
median_imputed = data.mask(data > (Q3 + 1.5 * IQR), data.median(), axis=1)

# Compare original vs winsorized data
plt.figure(figsize=(12, 6))
plt.subplot(1, 2, 1)
plt.boxplot(data['RELIANCE.NS'].dropna())
plt.title("Original Data")
plt.subplot(1, 2, 2)
plt.boxplot(winsorized_data['RELIANCE.NS'].dropna())
plt.title("Winsorized Data")
plt.tight_layout()
plt.show()
```

## Data Transformation

### Normalization and Scaling

```python
from sklearn.preprocessing import MinMaxScaler, StandardScaler, RobustScaler

# Min-Max Scaling (0 to 1)
minmax_scaler = MinMaxScaler()
data_minmax = pd.DataFrame(
    minmax_scaler.fit_transform(data),
    columns=data.columns,
    index=data.index
)

# Standardization (mean=0, std=1)
std_scaler = StandardScaler()
data_std = pd.DataFrame(
    std_scaler.fit_transform(data),
    columns=data.columns,
    index=data.index
)

# Robust Scaling (uses median and IQR, good for outliers)
robust_scaler = RobustScaler()
data_robust = pd.DataFrame(
    robust_scaler.fit_transform(data),
    columns=data.columns,
    index=data.index
)

# Visualize the effect of different scalers
plt.figure(figsize=(15, 10))
for i, (scaled_data, title) in enumerate([
    (data, 'Original'),
    (data_minmax, 'Min-Max'),
    (data_std, 'Standard'),
    (data_robust, 'Robust')
], 1):
    plt.subplot(2, 2, i)
    plt.hist(scaled_data['RELIANCE.NS'].dropna(), bins=30)
    plt.title(f"{title} Scaling")
plt.tight_layout()
plt.show()
```

## Handling Categorical Data

### Encoding Categorical Variables

```python
# Example: Adding a 'Trend' column based on price movement
data['Trend'] = np.where(data['RELIANCE.NS'].diff() > 0, 'Up', 'Down')

# 1. Label Encoding
from sklearn.preprocessing import LabelEncoder
le = LabelEncoder()
data['Trend_Encoded'] = le.fit_transform(data['Trend'])

# 2. One-Hot Encoding
trend_dummies = pd.get_dummies(data['Trend'], prefix='Trend')
data = pd.concat([data, trend_dummies], axis=1)

print("Encoded Trend column:")
print(data[['Trend', 'Trend_Encoded', 'Trend_Down', 'Trend_Up']].head())
```

## Date and Time Data

### Working with DateTime

```python
# Ensure index is datetime
data.index = pd.to_datetime(data.index)

# Extract date components
data['Year'] = data.index.year
data['Month'] = data.index.month
#data['Day'] = data.index.day
#data['DayOfWeek'] = data.index.dayofweek
#data['IsWeekend'] = data.index.dayofweek >= 5

# Resample to weekly data
weekly_data = data.resample('W').last()

# Calculate weekly returns
weekly_returns = weekly_data.pct_change().dropna()

print("Weekly Returns:")
print(weekly_returns.head())
```

## Text Data Cleaning

### Basic Text Cleaning

```python
# Example: Clean company names
company_names = [
    "  Reliance Industries Ltd. ",
    "Tata Consultancy Services (TCS) ",
    " HDFC Bank Ltd.  ",
    "Infosys Limited",
    "Hindustan Unilever Ltd"
]

import re

def clean_company_name(name):
    # Convert to string if not already
    name = str(name)
    # Remove extra whitespace
    name = ' '.join(name.split())
    # Remove common suffixes
    name = re.sub(r'\s*\([^)]*\)', '', name)  # Remove text in parentheses
    name = re.sub(r'\b(?:Ltd|Limited|Incorporated|Corp|Corporation|Inc|PLC)\b', '', name, flags=re.IGNORECASE)
    # Remove punctuation and extra spaces
    name = re.sub(r'[^\w\s]', '', name)
    name = ' '.join(name.split())
    return name.strip()

# Apply cleaning
cleaned_names = [clean_company_name(name) for name in company_names]
print("Original names:", company_names)
print("Cleaned names:", cleaned_names)
```

## Practice Exercises 📝

1. **Data Cleaning Pipeline**
   - Download stock data for 5 Indian companies for the last year
   - Create a function that takes the raw data and performs the following:
     - Handles missing values using appropriate methods
     - Detects and handles outliers
     - Adds new features like daily returns and moving averages
     - Saves the cleaned data to a new CSV file

2. **Time Series Imputation**
   - Create a synthetic time series dataset with missing values
   - Implement and compare different imputation methods:
     - Forward fill
     - Backward fill
     - Linear interpolation
     - Seasonal decomposition + imputation
   - Evaluate the methods using a holdout set

3. **Categorical Data Processing**
   - Create a dataset with mixed data types (numerical and categorical)
   - Implement a preprocessing pipeline that:
     - Handles missing values differently for numerical and categorical features
     - Encodes categorical variables appropriately
     - Scales numerical features
   - Compare the impact of different preprocessing choices on a simple model

## Next Steps

In the next module, we'll dive into Exploratory Data Analysis (EDA), where we'll learn how to explore and understand our cleaned data through visualization and statistical analysis.

🔙 [Previous: Data Collection](../3-data-collection/README.md) | 
🔜 [Next: Exploratory Data Analysis →](../5-exploratory-data-analysis/README.md)
