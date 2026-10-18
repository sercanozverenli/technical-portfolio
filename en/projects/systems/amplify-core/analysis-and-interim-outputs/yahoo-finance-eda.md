# Yahoo Finance Exploratory Data Analysis (Amplify Core)

## 1. Objective of Analysis and Data Architecture

The overarching vision of the **Amplify Core** system is to establish a model-agnostic data reliability verification and dynamic decision routing layer that audits heterogeneous data streams before they enter downstream machine learning models.

In this architecture, the **Financial Time Series (Yahoo Finance)** dataset was selected to represent high-frequency market fluctuations, sudden volatility clustering, fat-tailed risk regimes, and structural market breaks.

### Dataset Metadata
- **Target Assets**: 
  - **S&P 500 (`^GSPC`)**: The benchmark index of the US equity market, representing traditional capital markets (Low-to-moderate volatility, ~252 trading days per year).
  - **Bitcoin (`BTC-USD`)**: The premier 24/7 traded cryptocurrency (High volatility, 365 trading days per year).
- **Date Range**: September 17, 2014 – September 17, 2024 (10-Year Continuous Time Series).
- **Raw Record Volume**: 6,822 rows of historical market quotes (Open, High, Low, Close, Adj Close, Volume).

> **💡 HR & Executive Summary (Why These Two Assets?):**  
> One is a highly established traditional equity benchmark operating strictly 5 days a week during fixed trading hours (S&P 500), while the other is an ultra-volatile digital asset trading around the clock without weekends or market holidays (Bitcoin). Pairing these contrasting data regimes allows us to stress-test our AI reliability architecture across both tranquil market conditions and sudden systemic crisis shocks.

---

## 2. Raw Data Ingestion and Quality Sanitation

### 2.1. Database Extraction Pipeline

Raw market quotes stored in the PostgreSQL `yahoo_market_data` table were loaded into memory using the following extraction script:

```python
import psycopg2
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import skew, kurtosis

# 1. PostgreSQL connection configuration
DB_CONFIG = {
    "dbname": "amplify_core",
    "user": "postgres",
    "password": "password",
    "host": "localhost",
    "port": "5432"
}

# 2. SQL query execution for continuous market data
conn = psycopg2.connect(**DB_CONFIG)
query = """
    SELECT date, symbol, open, high, low, close, adj_close, volume
    FROM yahoo_market_data
    ORDER BY date ASC;
"""
df_raw = pd.read_sql(query, conn)
conn.close()

# 3. Shape and missingness inspection
print("Raw Shape:", df_raw.shape)
print("Missing Value Counts:\n", df_raw.isnull().sum())
```

### 2.2. Missingness Screening and Sanitation

During initial schema inspection, the `adj_close` (adjusted closing price) column was identified as **100% null (completely missing)**.

```python
# Compute column-wise null percentage
null_rates = df_raw.isnull().mean() * 100
print("Missingness Rates (%):\n", null_rates)

# Drop 100% null uninformative column
df_clean = df_raw.drop(columns=['adj_close'])
```

> **🛡️ Amplify Core Architectural Link (Multiplicative Veto Rule):**  
> In the Amplify Core DRS (Data Reliability Score) engine, our **Multiplicative Veto Filter ($V_j$)** immediately forces the reliability score of a feature to zero if 100% missingness is detected, preventing corrupt inputs from deceiving predictive models. Therefore, `adj_close` was purged before reaching downstream feature pipelines.

### 2.3. Base-100 Price Normalization

While the S&P 500 index traded in the 2,000 – 6,000 USD range, Bitcoin expanded from ~300 USD to over 70,000 USD across the same decade. To evaluate relative capital trajectories on a unified scale, **Base-100 Price Normalization** was applied:

$$P_{\text{norm}, t} = \left( \frac{P_t}{P_{t_0}} \right) \times 100$$

*Where $P_{t_0}$ represents the closing price on the shared inception date of September 17, 2014.*

```python
# Relative trajectory indexing with base 100
df_clean['norm_close'] = df_clean.groupby('symbol')['close'].transform(lambda x: (x / x.iloc[0]) * 100)
```

![Base-100 Normalized Price Trajectory](../../../../../docs/diagrams/amplify-core/01_yahoo_normalized_prices.png)

> **🔍 Chart Analysis Note (Top Plot):**  
> - **S&P 500 (Blue Curve)**: Grew steadily from base 100 to ~300 over 10 years, reflecting disciplined, low-risk capital expansion.
> - **Bitcoin (Orange Curve)**: Climbed logarithmically from base 100 to 25,000+, undergoing massive macro market cycles (2017 bull run, March 2020 liquidity shock, 2021 double peak, and 2024 all-time highs).

---

## 3. Logarithmic Returns and Volatility Modeling

### 3.1. Why Log Returns ($r_t$) Instead of Raw Prices?

Raw price series exhibit strong **non-stationarity** (time-dependent mean and variance). For statistical modeling and machine learning stability, price series must be differenced into stationary **Logarithmic Returns ($r_t$)**:

$$r_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(P_t) - \ln(P_{t-1})$$

```python
# Consecutive day logarithmic return calculation
df_clean['log_return'] = df_clean.groupby('symbol')['close'].transform(lambda x: np.log(x / x.shift(1)))
```

> **💡 Educational Insight (Why Log Returns Over Simple Percentage?):**  
> Simple arithmetic returns are non-additive over time (a +10% gain followed by a -10% drop leaves you at 99%, not break-even). Conversely, logarithmic returns are symmetric and time-additive ($\sum r_t$), preventing bias in compound mathematical calculations and risk models.

### 3.2. Annualized 30-Day Rolling Volatility

To track dynamic risk regimes over time, rolling standard deviations were computed over a 30-day window and scaled to annual terms according to each asset's distinct trading calendar:

$$\text{Volatility}_{\text{S\&P 500}} = \sigma_{30} \times \sqrt{252} \times 100$$
$$\text{Volatility}_{\text{BTC}} = \sigma_{30} \times \sqrt{365} \times 100$$

```python
def calc_annualized_vol(series, days_in_year=252, window=30):
    # Drop missing days to prevent calendar gap pollution
    clean_s = series.dropna()
    rolling_std = clean_s.rolling(window=window, min_periods=15).std()
    # Annualize by square root of trading days
    return rolling_std * np.sqrt(days_in_year) * 100

# Annualized rolling calculations for S&P 500 (252 days) and BTC (365 days)
sp500_returns = df_clean[df_clean['symbol'] == '^GSPC']['log_return']
btc_returns = df_clean[df_clean['symbol'] == 'BTC-USD']['log_return']

vol_sp500 = calc_annualized_vol(sp500_returns, days_in_year=252, window=30)
vol_btc = calc_annualized_vol(btc_returns, days_in_year=365, window=30)
```

> **⚠️ Critical Data Engineering Pitfall & Solution:**  
> Because equity markets close on weekends and holidays, merging S&P 500 and Bitcoin into a single DataFrame introduces calendar alignment gaps. Running `.rolling(30)` directly across merged rows introduces weekend `NaN` values, causing rolling windows to collapse into `NaN` for subsequent calculations. Sanitizing each asset's series with `.dropna()` independently prior to rolling calculations completely mitigates this artifact.

![30-Day Annualized Rolling Volatility](../../../../../docs/diagrams/amplify-core/01_yahoo_rolling_volatility.png)

### 3.3. Quantitative Volatility Findings
- **Bitcoin Volatility**: Typically fluctuates in the 40% – 80% corridor, surging to **~200% annualized** during liquidity crunches.
- **S&P 500 Volatility**: Averages between 10% and 20% (spiking to 85% during the March 2020 COVID-19 panic).
- **Result**: Bitcoin exhibits an average of **3.29x higher annualized volatility** compared to the S&P 500.

---

## 4. Return Distributions and Variance Regimes

To test for departures from normality and measure fat-tail risk, the 3rd and 4th statistical moments (Skewness and Excess Kurtosis) were evaluated alongside Kernel Density Estimations (KDE):

```python
# Compute distribution moments (Skewness and Kurtosis)
sp500_clean = sp500_returns.dropna()
btc_clean = btc_returns.dropna()

print(f"S&P 500 Skewness: {skew(sp500_clean):.4f}, Excess Kurtosis: {kurtosis(sp500_clean):.4f}")
print(f"Bitcoin Skewness: {skew(btc_clean):.4f}, Excess Kurtosis: {kurtosis(btc_clean):.4f}")

# Histogram and KDE distribution plot
fig, axes = plt.subplots(1, 2, figsize=(14, 5))
sns.histplot(sp500_clean, kde=True, ax=axes[0], color='#1f77b4', bins=60, stat="density")
axes[0].set_title("S&P 500 Log Return Distribution", fontweight='bold')

sns.histplot(btc_clean, kde=True, ax=axes[1], color='#ff7f0e', bins=60, stat="density")
axes[1].set_title("BTC-USD Log Return Distribution", fontweight='bold')
plt.tight_layout()
plt.show()
```

![Log Return Distributions (Histogram and KDE)](../../../../../docs/diagrams/amplify-core/01_yahoo_returns_distribution.png)

### Distribution Metrics Summary:
| Asset | Skewness | Excess Kurtosis | Statistical Interpretation |
|---|---|---|---|
| **S&P 500** | -0.065 | **11.896** | Symmetric center with extreme leptokurtic tail risk (sharp market drops) |
| **Bitcoin** | **-0.714** | **11.483** | Pronounced negative skewness (asymmetric sell-offs) and heavy fat tails |

> **🔬 Scientific Implication (Why Levene's Test and Dynamic Baselines?):**  
> Under a standard Gaussian distribution, excess kurtosis is approximately 0. Both financial series show excess kurtosis **above 11 (Leptokurtic / Fat-tailed)**, decisively disproving the classical "constant variance" assumption. This empirical evidence validates the necessity of Amplify Core's **Work Package 2 (WP 2)** components: **Variance Stability ($V_i$) via Levene's Test** and **Dynamic Baseline Calibration**.

---

## 5. Storage Architecture and Dataset Outputs

Sanitized data from Work Package 1 (WP 1) was stored under `data/processed/yahoo/` in two distinct schema configurations to support subsequent architectural phases:

```python
# 1. Long Format (Designed for record-level 7-indicator DRS evaluation)
df_clean.to_csv('data/processed/yahoo/yahoo_finance_clean_long.csv', index=False)

# 2. Pivot Format (Aligned time series for multi-asset routing benchmark)
df_pivot = df_clean.pivot(index='date', columns='symbol', values='close')
df_pivot.to_csv('data/processed/yahoo/yahoo_finance_clean_pivot.csv')
```

| Data File | Dimensions (Rows x Cols) | Architectural Role |
|---|---|---|
| `yahoo_finance_clean_long.csv` | 6,822 x 8 | Input pipeline for calculating record-level 7 DRS indicators (SNR, Missingness, CUSUM drift, etc.) |
| `yahoo_finance_clean_pivot.csv` | 4,113 x 2 (`date` index) | Historical asset alignment for Decision Routing Engine and benchmark comparisons |

---

→ [Next Stage: UJIIndoorLoc Exploratory Data Analysis](en/projects/systems/amplify-core/analysis-and-interim-outputs/ujiindoorloc-eda.md)
