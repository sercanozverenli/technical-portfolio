# Yahoo Finance Dataset

This directory contains the financial market dataset used in the **Amplify Core** experiments.

## Overview

The dataset consists of historical daily market data collected from Yahoo Finance using the Python **yfinance** library.

Two different financial assets were selected to represent different market characteristics:

- **BTC-USD** – Cryptocurrency market
- **^GSPC** – S&P 500 Index (U.S. stock market)

Using both assets allows the proposed reliability framework to be evaluated on financial time series with different volatility profiles and market dynamics.

## Time Period

**2014-01-01 → 2025-06-26**

This period was intentionally selected to include a broad range of market conditions, such as:

- Long-term bull markets
- Bear markets
- High-volatility periods
- Relatively stable market periods
- The COVID-19 market crash and recovery
- Cryptocurrency boom and correction cycles

Selecting a long and diverse historical period enables the proposed Data Reliability Score (DRS) framework to be evaluated under different market regimes rather than a single market condition.

## Data Collection

The dataset was generated using the following script:

```python
import yfinance as yf

df = yf.download(
    ["BTC-USD", "^GSPC"],
    start="2014-01-01",
    end="2025-06-26"
)

df.to_csv("btc_sp500_market_data_2014_2025.csv")
```

## File Structure

```text
raw/
└── btc_sp500_market_data_2014_2025.csv
```

The dataset contains daily market information provided by Yahoo Finance, including price and trading volume data for both assets.

## Reproducibility

Unlike the other datasets used in this project, this dataset was generated specifically for the experiments conducted in Amplify Core.

The exact version used during the experiments is included in this repository to ensure full reproducibility of the reported results.
