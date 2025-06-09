# 📥 Data Collection for Financial Analysis

## Table of Contents
1. [Introduction to Data Collection](#introduction-to-data-collection)
2. [APIs for Financial Data](#apis-for-financial-data)
3. [Web Scraping for Financial Data](#web-scraping-for-financial-data)
4. [Working with CSV and Excel Files](#working-with-csv-and-excel-files)
5. [Handling JSON Data](#handling-json-data)
6. [Best Practices for Data Collection](#best-practices-for-data-collection)
7. [Practice Exercises](#practice-exercises-)
8. [Next Steps](#next-steps)

## Introduction to Data Collection

Data collection is the first and most crucial step in any data analysis project. For financial analysis, we need reliable and accurate data. In this module, we'll explore various methods to collect financial data, focusing on Indian markets.

### Types of Financial Data

1. **Market Data**: Stock prices, indices, volumes
2. **Fundamental Data**: Company financials, ratios, earnings
3. **Economic Data**: GDP, inflation, interest rates
4. **Alternative Data**: News, social media, satellite images

### Data Quality Considerations

- **Accuracy**: Is the data correct and precise?
- **Completeness**: Are there missing values?
- **Timeliness**: How current is the data?
- **Consistency**: Is the data format consistent?
- **Reliability**: Is the source trustworthy?

## APIs for Financial Data

### 1. Yahoo Finance API (yfinance)

Yahoo Finance provides free stock market data through its API, accessible via the `yfinance` Python library.

#### Installation
```bash
pip install yfinance
```

#### Example: Fetching Stock Data

```python
import yfinance as yf
import pandas as pd

# Download Reliance Industries data
reliance = yf.download('RELIANCE.NS', start='2023-01-01', end='2023-12-31')
print(reliance.head())

# Get company info
ticker = yf.Ticker("RELIANCE.NS")
print(ticker.info['longBusinessSummary'])
print(f"Market Cap: {ticker.info['marketCap']}")
```

### 2. Alpha Vantage API

Alpha Vantage offers free and premium APIs for stock market data, technical indicators, and more.

#### Getting Started
1. Get a free API key from [Alpha Vantage](https://www.alphavantage.co/support/#api-key)
2. Install the Python package:
   ```bash
   pip install alpha_vantage
   ```

#### Example: Using Alpha Vantage

```python
from alpha_vantage.timeseries import TimeSeries

# Replace with your API key
API_KEY = 'YOUR_API_KEY'

# Initialize the TimeSeries class
ts = TimeSeries(key=API_KEY, output_format='pandas')

# Get daily data for Reliance
data, meta_data = ts.get_daily(symbol='RELIANCE.BSE', outputsize='compact')
print(data.head())

# Get technical indicators - SMA
from alpha_vantage.techindicators import TechIndicators
ti = TechIndicators(key=API_KEY, output_format='pandas')
sma, meta = ti.get_sma(symbol='RELIANCE.BSE', interval='daily', time_period=20)
print(sma.tail())
```

### 3. NSE India Data

For Indian market data, you can use the NSE's official website or third-party APIs.

#### Using NSE's Unofficial API

```python
import requests
import pandas as pd

# Get NIFTY 50 data
url = 'https://www.nseindia.com/api/equity-stockIndices?index=SECURITIES%20IN%20F%26O'

headers = {
    'User-Agent': 'Mozilla/5.0',
    'Accept-Language': 'en-US,en;q=0.9',
    'Accept-Encoding': 'gzip, deflate, br'
}

response = requests.get(url, headers=headers)
data = response.json()

# Convert to DataFrame
df = pd.DataFrame(data['data'])
print(df[['symbol', 'open', 'dayHigh', 'dayLow', 'lastPrice', 'pChange']].head())
```

## Web Scraping for Financial Data

Web scraping is useful when APIs are not available. We'll use `requests` and `BeautifulSoup` for scraping.

### Installation
```bash
pip install requests beautifulsoup4 lxml
```

### Example: Scraping Stock Data from Moneycontrol

```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# URL of the page to scrape
url = 'https://www.moneycontrol.com/india/stockpricequote/refineries/relianceindustries/RI'

# Send a GET request
headers = {
    'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/91.0.4472.124 Safari/537.36'
}
response = requests.get(url, headers=headers)
soup = BeautifulSoup(response.text, 'lxml')

# Extract stock price
price = soup.find('div', class_='inprice1').text.strip()
print(f"Current Price: {price}")

# Extract key metrics
metrics = {}
table = soup.find('table', class_='mctable1')
for row in table.find_all('tr'):
    cols = row.find_all('td')
    if len(cols) == 2:
        key = cols[0].text.strip()
        value = cols[1].text.strip()
        metrics[key] = value

print("\nKey Metrics:")
for key, value in metrics.items():
    print(f"{key}: {value}")
```

## Working with CSV and Excel Files

### Reading and Writing CSV Files

```python
import pandas as pd

# Read CSV
# From local file
df = pd.read_csv('stock_data.csv')

# From URL
url = 'https://query1.finance.yahoo.com/v7/finance/download/RELIANCE.NS?period1=1609459200&period2=1640995200&interval=1d&events=history'
df = pd.read_csv(url)

# Write to CSV
df.to_csv('reliance_data.csv', index=False)
```

### Working with Excel Files

```python
# Read Excel
# pip install openpyxl (for .xlsx files)
df = pd.read_excel('stock_data.xlsx', sheet_name='Sheet1')

# Write to Excel
df.to_excel('output.xlsx', sheet_name='Analysis', index=False)
```

## Handling JSON Data

### Working with JSON

```python
import json

# Parse JSON string
data = '{"stock": "RELIANCE", "price": 2500.50, "volume": 1000000}'
stock_data = json.loads(data)
print(stock_data['stock'])  # RELIANCE

# Convert dictionary to JSON
new_data = {'stock': 'TCS', 'price': 3200.75, 'volume': 2000000}
json_str = json.dumps(new_data, indent=4)
print(json_str)

# Read JSON file
with open('data.json', 'r') as f:
    data = json.load(f)

# Write to JSON file
with open('output.json', 'w') as f:
    json.dump(data, f, indent=4)
```

## Best Practices for Data Collection

1. **Respect Rate Limits**: Don't overload servers with too many requests
2. **Error Handling**: Always implement try-except blocks
3. **Caching**: Store data locally to avoid repeated requests
4. **User-Agent**: Set a proper User-Agent header
5. **Legal Considerations**: Check website's terms of service
6. **Data Validation**: Verify the integrity of collected data
7. **Documentation**: Document your data sources and collection methods

## Practice Exercises 📝

1. **API Data Collection**
   - Use the Alpha Vantage API to fetch daily data for 5 different Indian stocks
   - Calculate and compare their 50-day and 200-day moving averages
   - Create a DataFrame showing which stocks are in a "golden cross" (50-day MA above 200-day MA)

2. **Web Scraping**
   - Scrape the top gainers and losers from NSE website (https://www.nseindia.com/market-data/live-market-indices)
   - Store the data in a structured format (CSV/Excel)
   - Calculate the average percentage change for gainers and losers

3. **Data Cleaning and Integration**
   - Collect stock data from two different sources (e.g., Yahoo Finance and Alpha Vantage)
   - Merge the datasets on date
   - Identify and handle any discrepancies between the two data sources
   - Create a clean, unified dataset with source information

## Next Steps

In the next module, we'll dive into data cleaning and preprocessing techniques to prepare our collected data for analysis.

🔙 [Previous: Python Basics](../2-python-basics/README.md) | 
🔜 [Next: Data Cleaning →](../4-data-cleaning/README.md)
