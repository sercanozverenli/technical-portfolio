# Finansal Zaman Serisi Keşifsel Veri Analizi (Yahoo Finance EDA)

> **NOT:** Bu çalışma, geleneksel piyasa endeksleri (S&P 500) ile kripto varlıkların (Bitcoin) 10 yıllık (2014–2024) tarihsel verileri üzerinden gerçekleştirilen **Finansal Zaman Serisi Keşifsel Veri Analizi (EDA)** ve risk/oynaklık modellemesi pratiklerini sunmaktadır.

---

## 1. Giriş ve Veri Seti Tanımı

Finansal zaman serileri; yüksek frekanslı gürültü, durağan olmama (non-stationarity), oynaklık kümelenmesi (volatility clustering) ve ağır kuyruk (fat-tailed) dağılımları gibi kendine özgü zorluklar barındırır.

Bu analizde, iki farklı piyasa rejimini temsil eden varlıklar incelenmiştir:
- **S&P 500 (^GSPC)**: ABD borsa piyasalarının gösterge endeksi (Yılda ~252 işlem günü).
- **Bitcoin (BTC-USD)**: 7/24 işlem gören lider kripto para birimi (Yılda 365 işlem günü).

---

## 2. Zaman Serisi Ön İşleme ve Getiri Dönüşümleri

### 2.1. Fiyat Ölçekleme (Normalize Endeksleme)

Farklı meblağlardaki varlık fiyatlarını kıyaslamak amacıyla 100 bazlı normalizasyon uygulanmıştır:

$$P_{\text{norm}, t} = \frac{P_t}{P_0} \times 100$$

```python
import numpy as np
import pandas as pd
import matplotlib.pyplot as plt

# Varlık bazında 100 bazlı fiyat normalizasyonu
df['norm_close'] = df.groupby('symbol')['close'].transform(lambda x: (x / x.iloc[0]) * 100)

# Normalize Fiyat Grafiği Oluşturma
plt.figure(figsize=(12, 6))
for symbol, group in df.groupby('symbol'):
    plt.plot(group['date'], group['norm_close'], label=symbol)
plt.title("100 Bazlı Normalize Fiyat Gelişimi (2014-2024)")
plt.legend()
plt.show()
```

![Normalize Fiyat Gelişimi Grafiği](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_normalized_prices.png)

### 2.2. Logaritmik Getiri Hesabı ($r_t$)

Finansal analizde ham fiyat $P_t$ yerine durağanlaştırılmış **Logaritmik Getiri** kullanılır:

$$r_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(P_t) - \ln(P_{t-1})$$

```python
# Günlük logaritmik getirilerin hesaplanması
df['log_return'] = df.groupby('symbol')['close'].transform(lambda x: np.log(x / x.shift(1)))
```

> **NOT:** Doğrusal getiriler zamansal olarak toplanamazken ($\sum r_t$), logaritmik getiriler doğrudan toplanabilir niteliktedir. Ayrıca asimetrik fiyat yükseliş ve düşüşlerinde simetrik bileşik istatistik sağlar.

---

## 3. Yıllıklandırılmış Oynaklık ve İşlem Takvimi Hizalaması

### 3.1. Piyasa Takvimi Uyumsuzluklarının Yönetimi

- **Geleneksel Piyasalar**: Hafta sonları ve resmi tatiller kapalıdır ($\sqrt{252}$ çarpanı kullanılır).
- **Kripto Piyasalar**: 365 gün kesintisiz açıktır ($\sqrt{365}$ çarpanı kullanılır).

$$\text{Yıllık Volatilite} = \sigma_{30} \times \sqrt{T_{\text{işlem}}}$$

```python
# İşlem gün sayısına göre yıllıklandırılmış hareketli volatilite hesabı
def calculate_annualized_volatility(series, annual_trading_days):
    # Hafta sonu boşluklarından etkilenmemek için eksik değerler temizlenir
    clean_series = series.dropna()
    rolling_std = clean_series.rolling(window=30).std()
    return rolling_std * np.sqrt(annual_trading_days)

# S&P 500 için (252 gün)
sp500_vol = calculate_annualized_volatility(df[df['symbol']=='^GSPC']['log_return'], 252)

# BTC-USD için (365 gün)
btc_vol = calculate_annualized_volatility(df[df['symbol']=='BTC-USD']['log_return'], 365)
```

![30 Günlük Hareketli Volatilite Grafiği](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_rolling_volatility.png)

### 3.2. Volatilite Bulguları
- **Bitcoin Volatilitesi**: Ortalama **%60 – %120** bandında seyreder.
- **S&P 500 Volatilitesi**: Ortalama **%10 – %35** bandındadır.
- **Gözlem**: Bitcoin'in yıllıklandırılmış oynaklığı S&P 500'e kıyasla ortalama **3.29 kat daha yüksektir**.

---

## 4. İstatistiksel Dağılım Analizi (Skewness & Excess Kurtosis)

Getirilerin normal dağılıma uyup uymadığını test etmek için 3. ve 4. moment istatistikleri hesaplanmıştır:

$$\text{Skewness} = E\left[\left(\frac{X-\mu}{\sigma}\right)^3\right], \quad \text{Kurtosis} = E\left[\left(\frac{X-\mu}{\sigma}\right)^4\right]$$

```python
from scipy.stats import kurtosis, skew
import seaborn as sns

# Çarpıklık ve Aşırı Basıklık değerlerinin hesaplanması
sp500_returns = df[df['symbol']=='^GSPC']['log_return'].dropna()
btc_returns = df[df['symbol']=='BTC-USD']['log_return'].dropna()

print(f"S&P 500 Skewness: {skew(sp500_returns):.4f}, Excess Kurtosis: {kurtosis(sp500_returns):.4f}")
print(f"Bitcoin Skewness: {skew(btc_returns):.4f}, Excess Kurtosis: {kurtosis(btc_returns):.4f}")
```

![Getiri Dağılımları ve KDE Eğrileri](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_returns_distribution.png)

### İstatistiksel Sonuçlar
1. **Sol Çarpıklık (Left-Skewness)**: S&P 500 getirilerinde negatif çarpıklık gözlenmiştir. Piyasa yükselişleri yavaş ve kademeli olurken, düşüşlerin ani ve şiddetli gerçekleştiğini doğrular.
2. **Aşırı Basıklık (Leptokurtic / Fat-Tails)**: Bitcoin getirilerinde Excess Kurtosis > 5 çıkmıştır. Bu durum, piyasada Normal (Gauss) dağılımın öngördüğünden çok daha sık ekstrem uç değer (Outlier) oluştuğunu gösterir.

---

## 5. Çıkarılan Derlemeler ve Metodolojik Kazanımlar

1. **Veri Uyumlaştırma**: Farklı işlem takvimlerine sahip zaman serilerinde rolling window işlemleri yapılmadan önce varlık bazında bağımsız veri temizliği (`dropna()`) yapılmalıdır.
2. **Risk Ölçeklendirme**: Yıllıklandırılmış volatilite hesaplanırken her piyasanın kendi yıllık işlem günü sayısı ($\sqrt{252}$ vs $\sqrt{365}$) dikkate alınmalıdır.
3. **Model Varsayımları**: Finansal zaman serilerinde sabit varyans ve normal dağılım varsayımı geçersizdir; dinamik baseline kalibrasyonları ve Levene testi gibi varyans istikrarı kontrolleri zorunludur.
