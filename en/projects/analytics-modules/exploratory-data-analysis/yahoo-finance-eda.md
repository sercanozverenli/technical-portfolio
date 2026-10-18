# Financial Time Series Exploratory Data Analysis (Yahoo Finance EDA)

> **NOTE:** This project presents a **Standalone Financial Time Series Exploratory Data Analysis (EDA)**, return dynamics, and volatility modeling methodology utilizing 10 years (2014–2024) of historical market data across traditional equity benchmarks (S&P 500) and premier digital assets (Bitcoin).

---

## 1. Introduction and Data Architecture

Financial time series exhibit unique econometric challenges—including high-frequency market noise, non-stationarity, volatility clustering, and fat-tailed return distributions—that routinely invalidate standard machine learning and statistical assumptions.

In this study, two contrasting market regimes were examined:
- **S&P 500 (`^GSPC`)**: The benchmark index of the US stock market (Traditional capital markets, ~252 trading days per year).
- **Bitcoin (`BTC-USD`)**: The leading 24/7 traded cryptocurrency (Digital asset markets, 365 trading days per year).
- **Time Horizon**: September 17, 2014 – September 17, 2024 (10-Year Continuous Series).
- **Scope**: 6,822 raw market observation records (Open, High, Low, Close, Volume).

---

## 2. Time Series Preprocessing and Price Normalization

### 2.1. Price Scaling (Base-100 Indexing)

While the S&P 500 index fluctuated between 2,000 and 6,000 USD, Bitcoin expanded from ~300 USD to over 70,000 USD across the same decade. To evaluate the relative capital trajectory of these two disparate assets on a unified scale, **Base-100 Price Normalization** was implemented:

$$P_{\text{norm}, t} = \frac{P_t}{P_0} \times 100$$

*Where $P_0$ represents the closing price on the shared baseline inception date of September 17, 2014.*

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Compute relative growth trajectory indexed to base 100 on day zero
df['norm_close'] = df.groupby('symbol')['close'].transform(lambda x: (x / x.iloc[0]) * 100)

# Generate Normalized Price Visualization
plt.figure(figsize=(12, 6))
for symbol, group in df.groupby('symbol'):
    plt.plot(group['date'], group['norm_close'], label=symbol)
plt.title("Base-100 Normalized Price Trajectory (2014-2024)")
plt.xlabel("Date")
plt.ylabel("Normalized Price (Base=100)")
plt.legend()
plt.grid(True, alpha=0.3)
plt.show()
```

![Normalized Price Trajectory Plot](../../../../docs/diagrams/amplify-core/01_yahoo_normalized_prices.png)

> **🔍 Analytical Finding:**  
> The S&P 500 grew steadily from base 100 to ~300 over the decade, reflecting disciplined, low-volatility capital expansion. In contrast, Bitcoin surged logarithmically to 25,000+, undergoing massive macro liquidity cycles (2017 bull run, March 2020 liquidity shock, 2021 double top, and 2024 all-time highs).

---

## 3. Logarithmic Returns ($r_t$) and Volatility Modeling

### 3.1. Why Log Returns Instead of Raw Prices?

Raw price series are strictly **non-stationary**; their mean and variance change over time. For econometric models and statistical inference to remain valid, price series must be transformed into stationary **Logarithmic Returns ($r_t$)**:

$$r_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(P_t) - \ln(P_{t-1})$$

```python
# Compute daily logarithmic returns
df['log_return'] = df.groupby('symbol')['close'].transform(lambda x: np.log(x / x.shift(1)))
```

> **💡 Methodological Insight (Simple vs. Logarithmic Returns):**  
> Arithmetic percentage returns are not time-additive (e.g., a +10% gain followed by a -10% drop results in a net capital loss of 1%, leaving you at 99%). In contrast, logarithmic returns are symmetric and time-additive ($\sum r_t$). This property prevents distortion when calculating multi-period statistical moments and compound risk metrics.

### 3.2. Annualized 30-Day Rolling Volatility

To track evolving risk regimes across time, rolling standard deviations were computed over a 30-day window and scaled to annual terms according to each market's distinct trading calendar:

- **Traditional Markets**: Closed on weekends and federal holidays ($\sqrt{252}$ scaling factor).
- **Crypto Markets**: Open 24/7/365 without interruption ($\sqrt{365}$ scaling factor).

$$\text{Annualized Volatility}_{\text{S\&P 500}} = \sigma_{30} \times \sqrt{252} \times 100$$
$$\text{Annualized Volatility}_{\text{BTC}} = \sigma_{30} \times \sqrt{365} \times 100$$

```python
# Function to calculate calendar-adjusted annualized rolling volatility
def calculate_annualized_volatility(series, annual_trading_days, window=30):
    # Drop missing days to prevent calendar gap distortion
    clean_series = series.dropna()
    rolling_std = clean_series.rolling(window=window, min_periods=15).std()
    return rolling_std * np.sqrt(annual_trading_days) * 100

# Annualized rolling calculations for S&P 500 (252 days) and BTC (365 days)
sp500_vol = calculate_annualized_volatility(df[df['symbol']=='^GSPC']['log_return'], 252)
btc_vol = calculate_annualized_volatility(df[df['symbol']=='BTC-USD']['log_return'], 365)
```

> **⚠️ Critical Data Engineering Insight:**  
> Because equity markets close on weekends, joining equity and cryptocurrency into a single tabular schema creates calendar alignment gaps. Applying `.rolling()` directly across merged rows introduces weekend `NaN` entries, which propagate and cause rolling computations to collapse into `NaN`. Sanitizing each asset's series with `.dropna()` independently prior to rolling calculations completely resolves this pipeline issue.

![30-Day Annualized Rolling Volatility Plot](../../../../docs/diagrams/amplify-core/01_yahoo_rolling_volatility.png)

### 3.3. Empirical Volatility Findings
- **Bitcoin Volatility**: Fluctuates within the **40% – 80%** corridor during normal regimes, spiking to **~200%** during market-wide liquidity crunches.
- **S&P 500 Volatility**: Averages between **10% and 20%** (peaking at 85% during the March 2020 COVID-19 panic).
- **Comparative Ratio**: Bitcoin exhibits an average of **3.29x higher annualized volatility** compared to the S&P 500.

---

## 4. Statistical Distribution Analysis (Skewness & Excess Kurtosis)

To quantify departures from normality and measure fat-tail risk, the 3rd and 4th statistical moments were calculated alongside Kernel Density Estimations (KDE):

$$\text{Skewness} = E\left[\left(\frac{X-\mu}{\sigma}\right)^3\right], \quad \text{Excess Kurtosis} = E\left[\left(\frac{X-\mu}{\sigma}\right)^4\right] - 3$$

```python
from scipy.stats import kurtosis, skew
import seaborn as sns

sp500_returns = df[df['symbol']=='^GSPC']['log_return'].dropna()
btc_returns = df[df['symbol']=='BTC-USD']['log_return'].dropna()

print(f"S&P 500 Skewness: {skew(sp500_returns):.4f}, Excess Kurtosis: {kurtosis(sp500_returns):.4f}")
print(f"Bitcoin Skewness: {skew(btc_returns):.4f}, Excess Kurtosis: {kurtosis(btc_returns):.4f}")
```

![Return Distributions and KDE Curves](../../../../docs/diagrams/amplify-core/01_yahoo_returns_distribution.png)

### Statistical Distribution Metrics
| Asset | Skewness | Excess Kurtosis | Distribution Characteristics |
|---|---|---|---|
| **S&P 500** | -0.065 | **11.896** | Symmetric center with extreme leptokurtic tail risk (sudden panic sell-offs) |
| **Bitcoin** | **-0.714** | **11.483** | Pronounced negative skewness (asymmetric crash risk) and heavy fat tails |

1. **Negative Skewness (Left-Tailed Distribution)**: Bitcoin displays significant negative skewness (-0.714). This empirically validates that upward bull markets grind slowly over extended periods, while market declines are rapid, panic-driven, and severe.
2. **Excess Kurtosis (Leptokurtic / Fat Tails)**: Both assets exhibit Excess Kurtosis **exceeding 11** (a standard Gaussian distribution has an excess kurtosis of 0). This decisively refutes the classical "constant variance" and "normal distribution" assumptions in financial time series; extreme Black Swan events occur orders of magnitude more frequently than predicted by standard Gaussian curves.

---

## 5. Methodological Takeaways and EDA Conclusions

1. **Trading Calendar Harmonization**: Merging time series with differing market structures generates artificial `NaN` values. Rolling window statistics must be computed independently on each asset's active trading days.
2. **Risk Calibration**: Annualized volatility scaling must accurately reflect the underlying market frequency ($\sqrt{252}$ for equities vs. $\sqrt{365}$ for 24/7 digital assets).
3. **Dynamic Threshold Imperative**: Constant variance assumptions fail in financial applications. Production monitoring architectures require variance homogeneity testing (such as Levene's test) and adaptive calibration regimes to account for structural market breaks.
