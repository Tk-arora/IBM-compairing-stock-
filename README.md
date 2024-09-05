# IBM-compairing-stock-Python 
This repository provides tools for extracting and visualizing stock data using yfinance and web scraping. It includes functions to fetch historical stock prices and revenue data, clean and process this data, and plot it using Plotly for interactive visualization.

# README

## Project Overview
This project involves extracting stock data for Tesla (TSLA) and GameStop (GME), along with their respective revenue data using a combination of web scraping and the `yfinance` library. The goal is to visualize historical stock prices and revenues for both companies using `Plotly`.

## Prerequisites
To run this project, you will need to install the following libraries:
- `yfinance`
- `pandas`
- `requests`
- `beautifulsoup4`
- `plotly`

To install the necessary libraries, run:
```bash
pip install yfinance pandas requests beautifulsoup4 plotly
```

## Warning Filter
To ignore future warnings, we use the `warnings` module. This can be useful when warnings are irrelevant and clutter the output.
```python
import warnings
# Ignore all FutureWarnings
warnings.filterwarnings("ignore", category=FutureWarning)
```

## Function: `make_graph`
The `make_graph` function is responsible for plotting stock prices and revenue on a two-row subplot. It takes three inputs:
- **stock_data**: DataFrame containing stock data with at least `Date` and `Close` columns.
- **revenue_data**: DataFrame containing revenue data with `Date` and `Revenue` columns.
- **stock**: The name of the stock for labeling purposes.

### Code:
```python
from plotly.subplots import make_subplots
import plotly.graph_objects as go
import pandas as pd

def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing=0.3)
    
    stock_data_specific = stock_data[stock_data.Date <= '2021-06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    
    fig.update_layout(showlegend=False, height=900, title=stock, xaxis_rangeslider_visible=True)
    fig.show()
```

## Extracting Tesla Stock Data
To fetch Tesla stock data using `yfinance`:
```python
import yfinance as yf

# Create a ticker object for Tesla
tesla = yf.Ticker('TSLA')

# Extract historical stock data
tesla_data = tesla.history(period='max')

# Reset the index for better formatting
tesla_data.reset_index(inplace=True)
tesla_data.head()
```

## Web Scraping Tesla Revenue Data
Tesla's revenue data is extracted via web scraping using `requests` and `BeautifulSoup`.

### Code:
```python
import requests
from bs4 import BeautifulSoup
import pandas as pd

# Fetch HTML data
url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm'
html_data = requests.get(url).text

# Parse the data using BeautifulSoup
soup = BeautifulSoup(html_data, 'html5lib')

# Use pandas to extract the table
tables = pd.read_html(url)
tesla_revenue = tables[1]
tesla_revenue.columns = ['Date', 'Revenue']

# Clean the Revenue column
tesla_revenue['Revenue'] = tesla_revenue['Revenue'].str.replace(',|\$', "", regex=True)
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
tesla_revenue.tail()
```

## Extracting GameStop Stock Data
Similar to Tesla, we use `yfinance` to extract GameStop stock data.

### Code:
```python
# Create a ticker object for GameStop
gme = yf.Ticker('GME')

# Extract historical stock data
gme_data = gme.history(period='max')

# Reset the index for better formatting
gme_data.reset_index(inplace=True)
gme_data.head()
```

## Web Scraping GameStop Revenue Data
We use the same method for GameStop's revenue data as we did for Tesla.

### Code:
```python
# Fetch HTML data for GameStop revenue
url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html'
html_data = requests.get(url).text

# Parse the data using BeautifulSoup
soup = BeautifulSoup(html_data, 'html5lib')

# Use pandas to extract the table
tables = pd.read_html(url)
gme_revenue = tables[1]
gme_revenue.columns = ['Date', 'Revenue']

# Clean the Revenue column
gme_revenue['Revenue'] = gme_revenue['Revenue'].str.replace(',|\$', "", regex=True)
gme_revenue.tail()
```

## Plotting Tesla and GameStop Data
Finally, plot the stock and revenue data for Tesla and GameStop using the `make_graph` function.

### Tesla Plot:
```python
make_graph(tesla_data, tesla_revenue, 'Tesla Stock Data')
```

### GameStop Plot:
```python
make_graph(gme_data, gme_revenue, 'GameStop Stock Data')
```

