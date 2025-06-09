# 🤖 Machine Learning for Financial Analysis

## Table of Contents
1. [Introduction to ML in Finance](#introduction-to-ml-in-finance)
2. [Feature Engineering for Financial Data](#feature-engineering-for-financial-data)
3. [Supervised Learning: Regression](#supervised-learning-regression)
4. [Supervised Learning: Classification](#supervised-learning-classification)
5. [Unsupervised Learning: Clustering](#unsupervised-learning-clustering)
6. [Deep Learning for Financial Time Series](#deep-learning-for-financial-time-series)
7. [Model Evaluation and Backtesting](#model-evaluation-and-backtesting)
8. [Practice Exercises](#practice-exercises-)
9. [Next Steps](#next-steps)

## Introduction to ML in Finance

Machine Learning (ML) has become an essential tool in financial analysis, enabling us to extract insights, predict market movements, and automate trading strategies. This module covers various ML techniques applied to financial data.

### Common ML Applications in Finance

1. **Price Prediction**: Forecasting stock prices, indices, or currency rates.
2. **Algorithmic Trading**: Developing automated trading strategies.
3. **Risk Management**: Credit scoring, fraud detection, and risk assessment.
4. **Portfolio Management**: Asset allocation and optimization.
5. **Sentiment Analysis**: Analyzing news and social media for market sentiment.

### Setting Up the Environment

```python
import numpy as np
import pandas as pd
import yfinance as yf
import matplotlib.pyplot as plt
import seaborn as sns
from sklearn.model_selection import train_test_split, TimeSeriesSplit, GridSearchCV
from sklearn.preprocessing import StandardScaler, MinMaxScaler
from sklearn.metrics import mean_squared_error, r2_score, accuracy_score, classification_report, confusion_matrix, roc_curve, auc
from sklearn.linear_model import LinearRegression, LogisticRegression
from sklearn.ensemble import RandomForestRegressor, GradientBoostingRegressor, RandomForestClassifier
from sklearn.svm import SVR, SVC
from sklearn.cluster import KMeans
from sklearn.decomposition import PCA
import xgboost as xgb
import lightgbm as lgb
import tensorflow as tf
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense, LSTM, Dropout
from tensorflow.keras.optimizers import Adam
from tensorflow.keras.callbacks import EarlyStopping, ReduceLROnPlateau
import warnings
warnings.filterwarnings('ignore')

# Set style
plt.style.use('seaborn-v0_8-whitegrid') # Using a specific seaborn style
sns.set_palette("viridis")

# Download sample data (e.g., Reliance Industries)
data_ml = yf.download('RELIANCE.NS', start='2018-01-01', end='2023-12-31')
```

## Feature Engineering for Financial Data

Feature engineering is crucial for creating relevant input variables for ML models from raw financial data.

### Creating Technical Indicators

```python
def add_technical_indicators(df):
    df = df.copy()
    df['SMA_20'] = df['Close'].rolling(window=20).mean()
    df['SMA_50'] = df['Close'].rolling(window=50).mean()
    df['EMA_12'] = df['Close'].ewm(span=12, adjust=False).mean()
    df['EMA_26'] = df['Close'].ewm(span=26, adjust=False).mean()
    df['MACD'] = df['EMA_12'] - df['EMA_26']
    df['Signal_Line'] = df['MACD'].ewm(span=9, adjust=False).mean()
    delta = df['Close'].diff()
    gain = (delta.where(delta > 0, 0)).rolling(window=14).mean()
    loss = (-delta.where(delta < 0, 0)).rolling(window=14).mean()
    rs = gain / loss
    df['RSI'] = 100 - (100 / (1 + rs))
    df['Daily_Return'] = df['Close'].pct_change()
    df['Volatility_20D'] = df['Daily_Return'].rolling(window=20).std() * np.sqrt(252)
    df = df.dropna()
    return df

df_ml_features = add_technical_indicators(data_ml)
print(df_ml_features[['Close', 'SMA_20', 'RSI', 'MACD']].head())
```

## Supervised Learning: Regression

Predicting a continuous value, like future stock prices.

### Predicting Stock Prices

```python
# Prepare data for regression
X = df_ml_features.drop(['Open', 'High', 'Low', 'Close', 'Adj Close', 'Volume', 'Daily_Return'], axis=1, errors='ignore')
y = df_ml_features['Close'].shift(-1) # Predict next day's close

X = X[:-1] # Align X with y
y = y.dropna()

X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, shuffle=False) # Time series data, no shuffle

scaler = StandardScaler()
X_train_scaled = scaler.fit_transform(X_train)
X_test_scaled = scaler.transform(X_test)

# Linear Regression
lr_model = LinearRegression()
lr_model.fit(X_train_scaled, y_train)
lr_predictions = lr_model.predict(X_test_scaled)
lr_rmse = np.sqrt(mean_squared_error(y_test, lr_predictions))
print(f'Linear Regression RMSE: {lr_rmse:.2f}')

# Random Forest Regressor
rf_model_reg = RandomForestRegressor(n_estimators=100, random_state=42)
rf_model_reg.fit(X_train_scaled, y_train)
rf_predictions_reg = rf_model_reg.predict(X_test_scaled)
rf_rmse_reg = np.sqrt(mean_squared_error(y_test, rf_predictions_reg))
print(f'Random Forest RMSE: {rf_rmse_reg:.2f}')

# Plotting example for one model
plt.figure(figsize=(12, 6))
plt.plot(y_test.index, y_test, label='Actual Prices')
plt.plot(y_test.index, rf_predictions_reg, label='Predicted Prices (RF)', alpha=0.7)
plt.title('Stock Price Prediction (Random Forest)')
plt.legend()
plt.show()
```

## Supervised Learning: Classification

Predicting a categorical outcome, like whether a stock price will go up or down.

### Predicting Market Direction (Up/Down)

```python
# Prepare data for classification
df_ml_features['Price_Up'] = (df_ml_features['Close'].shift(-1) > df_ml_features['Close']).astype(int)
df_ml_features_cls = df_ml_features.dropna() # Drop NaN from shift

X_cls = df_ml_features_cls.drop(['Open', 'High', 'Low', 'Close', 'Adj Close', 'Volume', 'Daily_Return', 'Price_Up'], axis=1, errors='ignore')
y_cls = df_ml_features_cls['Price_Up']

X_train_cls, X_test_cls, y_train_cls, y_test_cls = train_test_split(X_cls, y_cls, test_size=0.2, shuffle=False)

scaler_cls = StandardScaler()
X_train_cls_scaled = scaler_cls.fit_transform(X_train_cls)
X_test_cls_scaled = scaler_cls.transform(X_test_cls)

# Logistic Regression
log_model = LogisticRegression(solver='liblinear') # Good for smaller datasets
log_model.fit(X_train_cls_scaled, y_train_cls)
log_predictions = log_model.predict(X_test_cls_scaled)
log_accuracy = accuracy_score(y_test_cls, log_predictions)
print(f'Logistic Regression Accuracy: {log_accuracy:.2f}')
print(classification_report(y_test_cls, log_predictions))

# Random Forest Classifier
rf_model_cls = RandomForestClassifier(n_estimators=100, random_state=42)
rf_model_cls.fit(X_train_cls_scaled, y_train_cls)
rf_predictions_cls = rf_model_cls.predict(X_test_cls_scaled)
rf_accuracy_cls = accuracy_score(y_test_cls, rf_predictions_cls)
print(f'Random Forest Classifier Accuracy: {rf_accuracy_cls:.2f}')
print(classification_report(y_test_cls, rf_predictions_cls))
```

## Unsupervised Learning: Clustering

Grouping similar stocks based on their characteristics (e.g., returns, volatility).

### Clustering Stocks

```python
# Download data for a few diverse Indian stocks
tickers = ['RELIANCE.NS', 'TCS.NS', 'HDFCBANK.NS', 'INFY.NS', 'ITC.NS', 'MARUTI.NS']
stock_data_list = []
for ticker in tickers:
    stock_data_list.append(yf.download(ticker, start='2020-01-01', end='2023-12-31')['Adj Close'])

all_stock_data = pd.concat(stock_data_list, axis=1)
all_stock_data.columns = tickers

daily_returns = all_stock_data.pct_change().dropna()

# Features: Annualized mean return and volatility
cluster_features = pd.DataFrame({
    'Mean_Return': daily_returns.mean() * 252,
    'Volatility': daily_returns.std() * np.sqrt(252)
})

scaler_cluster = StandardScaler()
features_scaled = scaler_cluster.fit_transform(cluster_features)

# K-Means Clustering
kmeans = KMeans(n_clusters=3, random_state=42, n_init=10)
cluster_features['Cluster'] = kmeans.fit_predict(features_scaled)

plt.figure(figsize=(10, 6))
sns.scatterplot(x='Volatility', y='Mean_Return', hue='Cluster', data=cluster_features, palette='viridis', s=100)
for i, txt in enumerate(cluster_features.index):
    plt.annotate(txt, (cluster_features['Volatility'][i], cluster_features['Mean_Return'][i]))
plt.title('Stock Clusters (Risk-Return Profile)')
plt.show()
```

## Deep Learning for Financial Time Series

Using neural networks, like LSTMs, for time series forecasting.

### LSTM for Price Prediction

```python
# Prepare data for LSTM
price_data = data_ml['Close'].values.reshape(-1, 1)
scaler_lstm = MinMaxScaler(feature_range=(0, 1))
scaled_price_data = scaler_lstm.fit_transform(price_data)

sequence_length = 60 # Use 60 days of historical data to predict the next day
X_lstm, y_lstm = [], []
for i in range(len(scaled_price_data) - sequence_length):
    X_lstm.append(scaled_price_data[i:i+sequence_length, 0])
    y_lstm.append(scaled_price_data[i+sequence_length, 0])

X_lstm, y_lstm = np.array(X_lstm), np.array(y_lstm)
X_lstm = np.reshape(X_lstm, (X_lstm.shape[0], X_lstm.shape[1], 1))

# Split data (80% train, 20% test)
train_size_lstm = int(len(X_lstm) * 0.8)
X_train_lstm, X_test_lstm = X_lstm[:train_size_lstm], X_lstm[train_size_lstm:]
y_train_lstm, y_test_lstm = y_lstm[:train_size_lstm], y_lstm[train_size_lstm:]

# Build LSTM model
lstm_model = Sequential([
    LSTM(units=50, return_sequences=True, input_shape=(X_lstm.shape[1], 1)),
    Dropout(0.2),
    LSTM(units=50, return_sequences=False),
    Dropout(0.2),
    Dense(units=25),
    Dense(units=1)
])

lstm_model.compile(optimizer='adam', loss='mean_squared_error')

# Train the model (use a small number of epochs for brevity in example)
# In practice, more epochs and callbacks like EarlyStopping would be used.
history = lstm_model.fit(X_train_lstm, y_train_lstm, epochs=10, batch_size=32, validation_data=(X_test_lstm, y_test_lstm), verbose=1)

# Make predictions
predictions_lstm_scaled = lstm_model.predict(X_test_lstm)
predictions_lstm = scaler_lstm.inverse_transform(predictions_lstm_scaled)

# Plot predictions
actual_prices_test = scaler_lstm.inverse_transform(y_test_lstm.reshape(-1,1))
plt.figure(figsize=(12,6))
plt.plot(actual_prices_test, color='blue', label='Actual Stock Price')
plt.plot(predictions_lstm, color='red', label='Predicted Stock Price (LSTM)', alpha=0.7)
plt.title('Stock Price Prediction using LSTM')
plt.xlabel('Time (Days in Test Set)')
plt.ylabel('Stock Price')
plt.legend()
plt.show()

lstm_rmse = np.sqrt(mean_squared_error(actual_prices_test, predictions_lstm))
print(f'LSTM RMSE: {lstm_rmse:.2f}')
```

## Model Evaluation and Backtesting

Evaluating model performance is critical. For trading strategies, backtesting simulates how a strategy would have performed on historical data.

- **Regression Metrics**: RMSE, MAE, R-squared.
- **Classification Metrics**: Accuracy, Precision, Recall, F1-score, ROC AUC.
- **Backtesting**: Involves simulating trades based on model signals, calculating portfolio returns, Sharpe ratio, max drawdown, etc. (More complex, often requires specialized libraries or custom code).

## Practice Exercises 📝

1.  **Feature Engineering**: Add more technical indicators (e.g., Bollinger Bands, ATR). Explore creating lagged features.
2.  **Hyperparameter Tuning**: Use `GridSearchCV` or `RandomizedSearchCV` to find optimal parameters for one of the regression or classification models.
3.  **Compare Models**: Implement another ML algorithm (e.g., XGBoost, LightGBM) for price prediction or classification and compare its performance.
4.  **LSTM Variations**: Experiment with different LSTM architectures (more layers, different number of units, different optimizers).
5.  **Clustering with More Features**: Add more features for stock clustering (e.g., P/E ratio, market cap - requires fetching fundamental data).

## Next Steps

In the next module, we'll dive into **Data Visualization**, where we'll learn how to create effective visualizations for financial data analysis and model results.

🔙 [Previous: Time Series Analysis](../6-time-series-analysis/README.md) |
🔜 [Next: Data Visualization →](../8-data-visualization/README.md)
