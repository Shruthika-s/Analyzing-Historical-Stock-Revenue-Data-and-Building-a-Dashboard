# Extracting and Visualizing Stock Data

## Description

This project demonstrates the extraction and visualization of stock data using Python. By leveraging the `yfinance` library and web scraping techniques, we retrieve historical stock prices and revenue data for companies like Tesla and GameStop. The data is then visualized to provide insights into stock performance and financial trends.

## Table of Contents

1. [Installation](#installation)
2. [Project Structure](#project-structure)
3. [Usage](#usage)
4. [Functions](#functions)
5. [Data Extraction](#data-extraction)
6. [Visualization](#visualization)

## Project Structure

The project consists of the following components:

- **Data Extraction**:
  - Using `yfinance` to extract stock data.
  - Web scraping to extract revenue data.
- **Visualization**:
  - Plotting stock prices and revenue data using Plotly.

## Usage

1. **Extract Stock Data**:
   - Utilize the `yfinance` library to download historical stock data.
   - Example:

     ```python
     import yfinance as yf

     # Create a ticker object for Tesla
     tesla_ticker = yf.Ticker("TSLA")

     # Extract historical stock data
     tesla_data = tesla_ticker.history(period="max")

     # Reset index to have 'Date' as a column
     tesla_data.reset_index(inplace=True)
     ```

2. **Extract Revenue Data**:
   - Use the `requests` library to fetch HTML content.
   - Parse the content with `BeautifulSoup` to locate and extract revenue tables.
   - Example:

     ```python
     import requests
     from bs4 import BeautifulSoup
     import pandas as pd

     # URL containing the revenue data
     url = "URL_TO_REVENUE_DATA"

     # Fetch the HTML content
     html_data = requests.get(url).text

     # Parse the HTML content
     soup = BeautifulSoup(html_data, "html.parser")

     # Locate the table containing revenue data
     tables = soup.find_all('table')
     revenue_data = pd.DataFrame(columns=["Date", "Revenue"])

     for table in tables:
         if "Company Quarterly Revenue" in table.text:
             for row in table.find("tbody").find_all("tr"):
                 cols = row.find_all("td")
                 date = cols[0].text
                 revenue = cols[1].text
                 revenue_data = pd.concat([revenue_data, pd.DataFrame({"Date": [date], "Revenue": [revenue]})], ignore_index=True)

     # Clean the Revenue column
     revenue_data["Revenue"] = revenue_data['Revenue'].str.replace(',|\$',"", regex=True)
     revenue_data.dropna(inplace=True)
     revenue_data = revenue_data[revenue_data['Revenue'] != ""]
     ```

3. **Visualization**:
   - Define a function to plot stock prices and revenue data using Plotly.
   - Example:

     ```python
     import plotly.graph_objects as go
     from plotly.subplots import make_subplots
     import pandas as pd

     def make_graph(stock_data, revenue_data, stock):
         fig = make_subplots(
             rows=2, cols=1, shared_xaxes=True,
             subplot_titles=("Historical Share Price", "Historical Revenue"),
             vertical_spacing=0.3
         )

         fig.add_trace(
             go.Scatter(
                 x=pd.to_datetime(stock_data['Date']),
                 y=stock_data['Close'].astype("float"),
                 name="Share Price"
             ),
             row=1, col=1
         )

         fig.add_trace(
             go.Scatter(
                 x=pd.to_datetime(revenue_data['Date']),
                 y=revenue_data['Revenue'].astype("float"),
                 name="Revenue"
             ),
             row=2, col=1
         )

         fig.update_xaxes(title_text="Date", row=1, col=1)
         fig.update_xaxes(title_text="Date", row=2, col=1)
         fig.update_yaxes(title_text="Price ($US)", row=1, col=1)
         fig.update_yaxes(title_text="Revenue ($US Millions)", row=2, col=1)
         fig.update_layout(
             showlegend=False,
             height=900,
             title=stock,
             xaxis_rangeslider_visible=True
         )
         fig.show()
     ```

4. **Plotting**:
   - Use the `make_graph` function to visualize the data.
   - Example:

     ```python
     make_graph(tesla_data, tesla_revenue, 'Tesla')
     ```

## Functions

- **make_graph**: Plots the historical share price and revenue data for a given stock.

## Data Extraction

- **Stock Data**: Extracted using the `yfinance` library.
- **Revenue Data**: Extracted via web scraping using `requests` and `BeautifulSoup`.

## Visualization

The project utilizes Plotly to create interactive visualizations of stock prices and revenue data.
