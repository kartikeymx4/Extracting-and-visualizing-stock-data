# Extracting-and-visualizing-stock-data

# Description
Extracting essential data from a dataset and displaying it is a necessary part of data science; therefore individuals can make correct decisions based on the data. In this assignment, you will extract some stock data, you will then display this data in a graph.

```python
import yfinance as yf
import pandas as pd
import requests
from bs4 import BeautifulSoup
import plotly.graph_objects as go
from plotly.subplots import make_subplots
```
```python
import warnings
# Ignore all warnings
warnings.filterwarnings("ignore", category=FutureWarning)
```
<h2>Define Graphing Function</h2>

```python
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
```
<h2>Use yfinance to extract stock data</h2>

```python
tesla=yf.Ticker("TSLA")
```
```python
tesla_data=tesla.history(period="max")
```

```python
tesla_data.reset_index(inplace=True)
tesla_data.head()
```

<h2>Use Webscraping to Extract Tesla Revenue Data</h2>
```python
url=" https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/revenue.htm"
html_data=requests.get(url).text
```
```python
soup=BeautifulSoup(html_data)
```
```python
tesla_revenue=pd.DataFrame(columns=["Date","Revenue"])
table=soup.find_all('table')[1]
for row in table.find("tbody").find_all("tr"):
    col= row.find_all('td')
    date= col[0].text
    revenue= col[1].text
    tesla_revenue= tesla_revenue.append({"Date":date, "Revenue":revenue}, ignore_index = True)
tesla_revenue["Revenue"]= tesla_revenue['Revenue'].str.replace('$','').str.replace(',','')  
```

```python
tesla_revenue["Revenue"] = tesla_revenue['Revenue'].str.replace(',|\$',"")
tesla_revenue.dropna(inplace=True)
tesla_revenue = tesla_revenue[tesla_revenue['Revenue'] != ""]
```

```python
tesla_revenue.tail()
```
<h1>GME Stock</h1>

```python
gme=yf.Ticker("GME")
gme_data=gme.history(period="max")
gme_data.reset_index(inplace=True)
gme_data.head()
```

```python
url=" https://cf-courses-data.s3.us.cloud-object-storage.appdomain.cloud/IBMDeveloperSkillsNetwork-PY0220EN-SkillsNetwork/labs/project/stock.html"
html_data=requests.get(url).text
soup=BeautifulSoup(html_data)
gme_revenue=pd.DataFrame(columns=["Date","Revenue"])
table=soup.find_all('table')[1]
for row in table.find("tbody").find_all("tr"):
    col= row.find_all('td')
    date= col[0].text
    revenue= col[1].text
    gme_revenue= gme_revenue.append({"Date":date, "Revenue":revenue}, ignore_index = True)
gme_revenue["Revenue"]= gme_revenue['Revenue'].str.replace('$','').str.replace(',','') 
gme_revenue["Revenue"] = gme_revenue['Revenue'].str.replace(',|\$',"")
gme_revenue.dropna(inplace=True)

gme_revenue = gme_revenue[gme_revenue['Revenue'] != ""]
gme_revenue.tail()
```

<h2>Plot Tesla Stock Graph</h2>

```python
make_graph(tesla_data,tesla_revenue,'Tesla')
```

<h2>Plot GameStop Stock Graph</h2>

```python
make_graph(gme_data, gme_revenue, 'GameStop')
```
