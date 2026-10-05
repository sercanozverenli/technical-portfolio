# Yahoo Finance Keşifsel Veri Analizi (Amplify Core)

## 1. Analizin Amacı ve Veri Mimarisi

Amplify Core sisteminin temel amacı, heterojen veri kaynaklarından gelen zaman serilerini işleyerek dinamik yönlendirme (routing) ve veri güvenilirliği skorlaması (DRS) gerçekleştirmektir. Finansal veri katmanı, yüksek frekanslı oynaklık ve rejim değişikliklerini temsil etmek üzere seçilmiştir.

### Veri Seti Metadataları
- **Hedef Varlıklar**: 
  - **S&P 500 (^GSPC)**: Geleneksel özkaynak piyasa endeksi (Düşük/Orta volatilite, 252 işlem günü/yıl).
  - **Bitcoin (BTC-USD)**: Kripto varlık (Yüksek volatilite, 365 işlem günü/yıl).
- **Zaman Aralığı**: 17 Eylül 2014 – 17 Eylül 2024 (10 Yıllık Kesintisiz Zaman Serisi).
- **Kayıt Hacmi**: 6.822 Satır ham kayıt.

---

## 2. Ham Veriye Erişim ve Veri Kalitesi Temizliği

### 2.1. Veri Tabanı Bağlantısı ve Yükleme Scripti

PostgreSQL veritabanından ham piyasa verilerinin çekilmesi ve ilk yapısal incelemesi aşağıdaki script ile gerçekleştirilmiştir:

```python
import psycopg2
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import skew, kurtosis

# PostgreSQL veritabanı bağlantı parametreleri
DB_CONFIG = {
    "dbname": "amplify_core",
    "user": "postgres",
    "password": "password",
    "host": "localhost",
    "port": "5432"
}

# Ham verilerin çekilmesi
conn = psycopg2.connect(**DB_CONFIG)
query = """
    SELECT date, symbol, open, high, low, close, adj_close, volume
    FROM yahoo_market_data
    ORDER BY date ASC;
"""
df_raw = pd.read_sql(query, conn)
conn.close()

# Ham veri boyutları ve sütun durumları
print("Ham Veri Boyutu:", df_raw.shape)
print("Eksik Değer Sayıları:\n", df_raw.isnull().sum())
```

### 2.2. Eksiklik (Missingness) Tespiti ve Temizliği

Ham veritabanı sorgusunda yapılan veri sağlığı taramasında `adj_close` (düzeltilmiş kapanış fiyatı) sütununun **%100 oranında null** olduğu saptanmıştır.

```python
# Veri seti sütunlarındaki eksik değer (null) oranlarının tespiti
null_rates = df_raw.isnull().mean() * 100
print("Eksiklik Oranları (%):\n", null_rates)

# %100 null olan adj_close sütununun veri setinden çıkarılması
df_clean = df_raw.drop(columns=['adj_close'])
```

DRS katmanının *Çarpımsal Veto Filtresi* kuralı uyarınca, %100 eksiklik içeren bir sütun sisteme girdiğinde tüm güvenilirlik skorunu sıfırlar. Bu nedenle `adj_close` analize dahil edilmeyip veri setinden tamamen çıkarılmıştır.

### 2.3. Fiyat Normalizasyonu (100 Bazlı Endeksleme)

Farklı birim ve ölçeklerdeki varlıkların (S&P 500 ~2.000-5.500 USD, BTC ~300-70.000 USD) fiyat hareketlerini aynı grafikte karşılaştırabilmek için 100 bazlı endeksleme uygulanmıştır:

$$P_{\text{norm}, t} = \left( \frac{P_t}{P_{t_0}} \right) \times 100$$

```python
# Her varlığın ilk günkü fiyatına göre 100 bazlı endekslenmesi
df_clean['norm_close'] = df_clean.groupby('symbol')['close'].transform(lambda x: (x / x.iloc[0]) * 100)
```

![Normalize Fiyat Karşılaştırması (100 Bazlı)](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_normalized_prices.png)

---

## 3. Logaritmik Getiri ve Oynaklık (Volatilitik) Analizi

### 3.1. Logaritmik Getiri ($r_t$) Hesaplaması

Finansal zaman serilerinde ham fiyatlar durağan (stationary) olmadığından, analizler **Logaritmik Getiri** üzerinden yürütülmüştür:

$$r_t = \ln\left(\frac{P_t}{P_{t-1}}\right)$$

```python
# Zaman serisinde ardışık günler arasındaki logaritmik getirinin hesaplanması
df_clean['log_return'] = df_clean.groupby('symbol')['close'].transform(lambda x: np.log(x / x.shift(1)))
```

> **NOT:** Doğrusal getiriler zamansal olarak toplanamazken ($\sum r_t$), logaritmik getiriler doğrudan toplanabilir niteliktedir. Ayrıca asimetrik fiyat yükseliş ve düşüşlerinde simetrik bileşik istatistik sağlar.

### 3.2. Yıllıklandırılmış 30 Günlük Hareketli Oynaklık (Annualised Volatility)

Varlıkların risk rejimlerini izlemek için 30 günlük pencerelerde hareketli standart sapma hesaplanmış ve işlem takvimlerine göre yıllıklandırılmıştır:

$$\text{Volatilitik}_{\text{S\&P 500}} = \sigma_{30} \times \sqrt{252}$$
$$\text{Volatilitik}_{\text{BTC}} = \sigma_{30} \times \sqrt{365}$$

```python
# Bağımsız seriler üzerinde yıllıklandırılmış hareketli volatilite hesabı
def calc_annualized_vol(series, days_in_year=252, window=30):
    # Serideki eksik değerler temizlenerek kayan pencere uygulanır
    clean_s = series.dropna()
    rolling_std = clean_s.rolling(window=window).std()
    return rolling_std * np.sqrt(days_in_year)

# S&P 500 ve BTC için ayrı ayrı hesaplama
sp500_returns = df_clean[df_clean['symbol'] == '^GSPC']['log_return']
btc_returns = df_clean[df_clean['symbol'] == 'BTC-USD']['log_return']

vol_sp500 = calc_annualized_vol(sp500_returns, days_in_year=252, window=30)
vol_btc = calc_annualized_vol(btc_returns, days_in_year=365, window=30)
```

> **NOT:** S&P 500 hafta sonları ve resmi tatillerde işlem görmediği için verisinde doğal tarih boşlukları mevcuttur. Çoklu varlık içeren bir DataFrame üzerinde doğrudan `.rolling(30).std()` çağrısı yapıldığında, hafta sonuna denk gelen `NaN` değerleri kayan pencere içerisine girerek takip eden 5 işlem gününün tamamının `NaN` olarak hesaplanmasına yol açmaktadır. Bu durum, her varlığın kendi getirisi üzerinden `.dropna()` uygulanıp bağımsız seriler halinde volatilite hesabı yapılmasıyla tamamen çözülmüştür.

![30 Günlük Yıllıklandırılmış Volatilite](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_rolling_volatility.png)

### 3.3. Volatilite Bulguları
- **Bitcoin Oynaklığı**: Ortalama %60 – %120 aralığındadır.
- **S&P 500 Oynaklığı**: Ortalama %10 – %35 aralığındadır (2020 şokunda peak: %75).
- **Oran**: Bitcoin, S&P 500'e kıyasla ortalama **3.29 kat daha yüksek oynaklığa** sahiptir.

---

## 4. İstatistiksel Dağılım ve Varyans Rejimleri

Getiri dağılımlarının Skewness (Çarpıklık) ve Excess Kurtosis (Aşırı Basıklık) değerleri aşağıdaki script ile hesaplanıp histogram/KDE grafikleri oluşturulmıştır:

```python
# Dağılım Momentlerinin Hesaplanması
sp500_clean = sp500_returns.dropna()
btc_clean = btc_returns.dropna()

print(f"S&P 500 Skewness: {skew(sp500_clean):.4f}, Excess Kurtosis: {kurtosis(sp500_clean):.4f}")
print(f"Bitcoin Skewness: {skew(btc_clean):.4f}, Excess Kurtosis: {kurtosis(btc_clean):.4f}")

# Histogram ve KDE Görselleştirme Scripti
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

sns.histplot(sp500_clean, kde=True, ax=axes[0], color='blue', bins=50)
axes[0].set_title("S&P 500 Log Getiri Dağılımı")

sns.histplot(btc_clean, kde=True, ax=axes[1], color='orange', bins=50)
axes[1].set_title("BTC-USD Log Getiri Dağılımı")

plt.tight_layout()
plt.show()
```

![Log Getiri Dağılımları (Histogram ve KDE)](../../../../../docs/assets/diagrams/amplify-core/01_yahoo_returns_distribution.png)

> **NOT:** Sabit varyans varsayımının finansal veride geçerli olmadığı kanıtlanmıştır. DRS Katmanı'nın **Varyans İstikrabı ($V_i$)** metriği için **Levene Testi** ve **Dinamik Baseline Kalibrasyonu** uygulanması zorunluluğu belgelenmiştir.

---

## 5. Çıktı Veri Formatları ve Veri Saklama Scripti

İş Paketi 1 çıktısı olarak temizlenen veriler, `data/processed/yahoo/` altında iki farklı formatta saklanmıştır:

```python
# Long Format Dışa Aktarımı (DRS 7 Gösterge Motoru İçin)
df_clean.to_csv('data/processed/yahoo/yahoo_finance_clean_long.csv', index=False)

# Pivot Format Dışa Aktarımı (Yönlendirme Motoru İçin)
df_pivot = df_clean.pivot(index='date', columns='symbol', values='close')
df_pivot.to_csv('data/processed/yahoo/yahoo_finance_clean_pivot.csv')
```

| Veri Dosyası | Satır x Sütun | Kullanım Alanı |
|---|---|---|
| `yahoo_finance_clean_long.csv` | 6.822 x 9 | Tekil kayıt bazlı 7 DRS göstergesi (SNR, Missingness vb.) hesaplama motoru için |
| `yahoo_finance_clean_pivot.csv` | 4.113 x 2 (`date` indeksli) | Yönlendirme Motoru (Routing Engine) ve Model Kıyaslama testlerinde çoklu varlık hizalaması için |

---

→ [Bir Sonraki Aşama: UJIIndoorLoc Keşifsel Veri Analizi](tr/projects/systems/amplify-core/analysis-and-interim-outputs/ujiindoorloc-eda.md)
