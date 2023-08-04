# WebscrapingVisuals

import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots

def make_graph(stock_data, revenue_data, stock):
    fig = make_subplots(rows=2, cols=1, shared_xaxes=True, subplot_titles=("Historical Share Price", "Historical Revenue"), vertical_spacing = .3)
    stock_data_specific = stock_data[stock_data.Date <= '2021--06-14']
    revenue_data_specific = revenue_data[revenue_data.Date <= '2021-04-30']
    fig.add_trace(go.Scatter(x=pd.to_datetime(stock_data_specific.Date, infer_datetime_format=True), y=stock_data_specific.Close.astype("float"), name="Share Price"), row=1, col=1)
    fig.add_trace(go.Scatter(x=pd.to_datetime(revenue_data_specific.Date, infer_datetime_format=True), y=revenue_data_specific.Revenue.astype("float"), name="Revenue"), row=2, col=1)
    fig.update_xaxes(title_text="Date", row=1, col=1)
    fig.update_xaxes(title_text="Date", row=2, col=1)
    fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
    fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
    fig.update_layout(showlegend=False,
    height=900,
    title=stock,
    xaxis_rangeslider_visible=True)
    fig.show()


# Question 1: yfinance to Extract Stock Data
Tesla = yf.Ticker("TSLA")
tesla_data = Tesla.history(period="max")
tesla_data.reset_index(inplace=True)
print(tesla_data.head())


# Question 2 Webscraping Tesla Revenue Data
# https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm
url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm'
html_data  = requests.get(url).text
soup = BeautifulSoup(html_data, 'html5lib')
read_html_pandas_data = pd.read_html(url)
tesla_revenue = read_html_pandas_data[1]
#Remove comma and dollar sign
tesla_revenue['Tesla Quarterly Revenue (Millions of US $).1'] = tesla_revenue['Tesla Quarterly Revenue (Millions of US $).1'].str.replace(',|\$', '', regex=True)
#Remove null or empty strings
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Tesla Quarterly Revenue (Millions of US $).1'] != ""]
# Relabel columns for clarity
tesla_revenue.rename(columns={'Tesla Quarterly Revenue (Millions of US $)': 'Date'}, inplace=True)
tesla_revenue.rename(columns={'Tesla Quarterly Revenue (Millions of US $).1': 'Revenue'}, inplace=True)
# using the tail function to display the last 5 rows
print(tesla_revenue.tail())

# Question 3 Use yfinance to Extract Stock Data
GameStop= yf.Ticker("GME")
gme_data = GameStop.history(period="max")
gme_data.reset_index(inplace=True)
print(gme_data.head())


# Question 4 Webscraping GME Revenue
url = 'https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html'
html_data  = requests.get(url).text
soup = BeautifulSoup(html_data, 'html5lib')
read_html_pandas_data = pd.read_html(url)
gme_revenue = read_html_pandas_data[1]
#Remove comma and dollar sign
gme_revenue['GameStop Quarterly Revenue (Millions of US $).1'] = gme_revenue['GameStop Quarterly Revenue (Millions of US $).1'].str.replace(',|\$', '', regex=True)
#Remove null or empty strings
gme_revenue.dropna(inplace=True)
# Relabel columns for clarity
gme_revenue = gme_revenue[gme_revenue['GameStop Quarterly Revenue (Millions of US $).1'] != ""]
gme_revenue.rename(columns={'GameStop Quarterly Revenue (Millions of US $)': 'Date'}, inplace=True)
gme_revenue.rename(columns={'GameStop Quarterly Revenue (Millions of US $).1': 'Revenue'}, inplace=True)
# using the tail function to display the last 5 rows
print(gme_revenue.tail())


# Question 5 Plot Tesla Stock Graph
make_graph(tesla_data, tesla_revenue, 'Tesla')


# Question 6 Plot GameStop Graph
make_graph(gme_data, gme_revenue, 'GameStop')
