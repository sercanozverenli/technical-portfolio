# Yahoo Finance Keşifsel Veri Analizi (Amplify Core)

## 1. Analizin Amacı ve Veri Mimarisi

**Amplify Core** sisteminin temel vizyonu, heterojen veri akışlarını makine öğrenmesi modellerine girmeden önce denetleyen modelden bağımsız (*model-agnostic*) bir veri güvenilirliği ve dinamik karar yönlendirme katmanı oluşturmaktır. 

Bu mimaride **Finansal Zaman Serisi (Yahoo Finance)** veri seti; yüksek frekanslı piyasa dalgalanmalarını, ani oynaklık patlamalarını (*volatility clustering*), rejim değişikliklerini ve kuyruk risklerini temsil etmek üzere seçilmiştir.

### Veri Seti Metadataları
- **Hedef Varlıklar**: 
  - **S&P 500 (`^GSPC`)**: ABD borsa piyasalarının gösterge endeksi. Geleneksel sermaye piyasalarını temsil eder (Düşük/Orta oynaklık, yılda ~252 işlem günü).
  - **Bitcoin (`BTC-USD`)**: 7/24 işlem gören lider kripto para birimi (Yüksek oynaklık, yılda 365 işlem günü).
- **Tarih Aralığı**: 17 Eylül 2014 – 17 Eylül 2024 (10 Yıllık Kesintisiz Zaman Serisi).
- **Ham Kayıt Hacmi**: 6.822 Satır piyasa kaydı (Açılış, Yüksek, Düşük, Kapanış, Hacim).

### Varlık Seçim Rasyoneli
Portföyde S&P 500 ve Bitcoin'in birlikte konumlandırılması, iki uç piyasa rejiminin (geleneksel sermaye piyasalarının düşük oynaklıklı ve regüle yapısı ile 7/24 kesintisiz çalışan dijital varlıkların likidite şokları) eşzamanlı olarak modellenmesini sağlar. Bu zıt karakterdeki veriler, Amplify Core veri güvenilirlik katmanının hem sakin piyasalarda hem de kriz anlarında doğru çalışabilirliğini doğrulamak üzere sisteme dahil edilmiştir.

---

## 2. Ham Veriye Erişim ve Veri Kalitesi Temizliği

### 2.1. Veri Tabanı Bağlantısı ve Veri Çekme Scripti

PostgreSQL veritabanında saklanan `yahoo_market_data` tablosundan ham piyasa verileri aşağıdaki sorgu ile bellege aktarılmıştır:

```python
import psycopg2
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
from scipy.stats import skew, kurtosis

# 1. PostgreSQL veritabanı bağlantı parametreleri
DB_CONFIG = {
    "dbname": "amplify_core",
    "user": "postgres",
    "password": "password",
    "host": "localhost",
    "port": "5432"
}

# 2. Ham piyasa verilerinin SQL ile çekilmesi
conn = psycopg2.connect(**DB_CONFIG)
query = """
    SELECT date, symbol, open, high, low, close, adj_close, volume
    FROM yahoo_market_data
    ORDER BY date ASC;
"""
df_raw = pd.read_sql(query, conn)
conn.close()

# 3. Ham veri boyutları ve sütun durumları
print("Ham Veri Boyutu:", df_raw.shape)
print("Eksik Değer Sayıları:\n", df_raw.isnull().sum())
```

### 2.2. Eksik Veri Analizi ve Sütun Tasfiyesi

Veritabanından çekilen ham veride yapılan eksiklik taramasında, `adj_close` (düzeltilmiş kapanış fiyatı) sütununun **%100 oranında boş (null)** olduğu tespit edilmiştir.

```python
# Veri seti sütunlarındaki eksik değer oranlarının analizi
null_rates = df_raw.isnull().mean() * 100
print("Eksiklik Oranları (%):\n", null_rates)

# %100 null olan ve bilgi taşımayan adj_close sütununun kaldırılması
df_clean = df_raw.drop(columns=['adj_close'])
```

> **Veri Güvenilirliği (DRS) Tasarım Kararı:**  
> Amplify Core DRS katmanında kurgulanan **Çarpımsal Veto Filtresi ($V_j$)**, kritik bir veri sütununda tam eksiklik (%100 null) saptandığında ilgili özelliğe ait güvenilirlik skorunu derhal sıfıra indirir. Bu mekanizma, hatalı veya bilgi taşımayan girdilerin karar motoruna beslenmesini engellemek amacıyla `adj_close` alanının ham aşamada veri setinden tamamen tasfiye edilmesini sağlamıştır.

### 2.3. Fiyat Normalizasyonu (100 Bazlı Endeksleme)

S&P 500 endeksi 2.000 – 6.000 USD bandında hareket ederken, Bitcoin aynı 10 yıllık dönemde 300 USD seviyelerinden 70.000+ USD seviyelerine tırmanmıştır. Bu iki farklı ölçekteki varlığı tek bir eksende karşılaştırabilmek amacıyla **100 Bazlı Normalizasyon** uygulanmıştır:

$$P_{\text{norm}, t} = \left( \frac{P_t}{P_{t_0}} \right) \times 100$$

*Burada $P_{t_0}$, iki varlığın ortak başlangıç tarihi olan 17 Eylül 2014 tarihindeki ilk kapanış fiyatını ifade eder.*

```python
# Her iki varlığın ilk günkü fiyatı 100 kabul edilerek göreceli büyüme oranı hesaplanır
df_clean['norm_close'] = df_clean.groupby('symbol')['close'].transform(lambda x: (x / x.iloc[0]) * 100)
```

![Normalize Fiyat Karşılaştırması (100 Bazlı)](../../../../../docs/diagrams/amplify-core/01_yahoo_normalized_prices.png)

### Fiyat Gelişimi ve Tarihsel Rejim Gözlemleri
- **S&P 500 (Mavi Hat)**: 10 yıllık süreçte 100 bazından ~300 bazına yükselerek, geleneksel sermaye piyasalarının düşük riskli ve dengeli büyümesini yansıtmıştır.
- **Bitcoin (Turuncu Hat)**: Logaritmik eksende 100 bazından 25.000+ seviyesine çıkarak devasa makro piyasa döngüleri (2017 boğası, Mart 2020 likidite çöküşü, 2021 çift zirvesi ve 2024 rekorları) üretmiştir.

---

## 3. Logaritmik Getiri ve Oynaklık (Volatilite) Modellemesi

### 3.1. Getiri Metriği Rasyoneli: Logaritmik Dönüşümün Matematiksel Temeli

Finansal piyasalarda ham fiyat serileri **durağan değildir (non-stationary)**; yani zamanla ortalaması ve varyansı değişir. Makine öğrenmesi modellerinin sağlıklı çalışabilmesi için serinin durağanlaştırılması gerekir. Bu amaçla ardışık günler arasındaki **Logaritmik Getiri ($r_t$)** hesaplanmıştır:

$$r_t = \ln\left(\frac{P_t}{P_{t-1}}\right) = \ln(P_t) - \ln(P_{t-1})$$

```python
# Zaman serisinde ardışık günler arasındaki logaritmik getiri hesabı
df_clean['log_return'] = df_clean.groupby('symbol')['close'].transform(lambda x: np.log(x / x.shift(1)))
```

Doğrusal yüzdesel getiriler geriye dönük zamansal olarak toplanamazken (örneğin %10 artışın ardından gelen %10 düşüş net sermayeyi %99'a düşürür), logaritmik getiriler simetriktir ve zamansal olarak doğrudan toplanabilir niteliktedir ($\sum r_t$). Bu sayede zaman serisi modellemelerinde yapay istatistiksel sapmaları engeller.

### 3.2. Yıllıklandırılmış 30 Günlük Hareketli Oynaklık (Annualized Rolling Volatility)

Varlıkların risk derecesini anlık izleyebilmek için 30 günlük kayan pencerede (*rolling window*) standart sapma hesaplanmış ve işlem takvimlerine göre yıllıklandırılmıştır:

$$\text{Volatilite}_{\text{S\&P 500}} = \sigma_{30} \times \sqrt{252} \times 100$$
$$\text{Volatilite}_{\text{BTC}} = \sigma_{30} \times \sqrt{365} \times 100$$

```python
def calc_annualized_vol(series, days_in_year=252, window=30):
    # Eksik günler temizlenerek 30 günlük hareketli standart sapma alınır
    clean_s = series.dropna()
    rolling_std = clean_s.rolling(window=window, min_periods=15).std()
    # Yıllık bazda işlem günü karekökü ile çarpılarak normalize edilir
    return rolling_std * np.sqrt(days_in_year) * 100

# S&P 500 (252 gün) ve BTC (365 gün) serileri üzerinde hesaplama
sp500_returns = df_clean[df_clean['symbol'] == '^GSPC']['log_return']
btc_returns = df_clean[df_clean['symbol'] == 'BTC-USD']['log_return']

vol_sp500 = calc_annualized_vol(sp500_returns, days_in_year=252, window=30)
vol_btc = calc_annualized_vol(btc_returns, days_in_year=365, window=30)
```

### İşlem Takvimi Uyumsuzluğu ve Kayan Pencere (Rolling Window) Hizalaması
S&P 500 borsa tatillerinde kapalı olduğundan takviminde doğal boşluklar vardır. Veri seti tek bir DataFrame içindeyken doğrudan `.rolling(30)` uygulandığında, S&P 500'deki hafta sonu boşlukları pencereye dahil olarak sonraki hesaplamalarda yapay `NaN` yayılmasına yol açar. Bu hatayı engellemek adına her varlık kendi işlem takviminde bağımsız olarak `.dropna()` yapıldıktan sonra kayan pencereye tabi tutulmuştur.

![30 Günlük Yıllıklandırılmış Volatilite](../../../../../docs/diagrams/amplify-core/01_yahoo_rolling_volatility.png)

### 3.3. Volatilite Bulguları ve Sayısal Kanıtlar
- **Bitcoin Oynaklığı**: Normal dönemlerde %40 – %80 bandında seyrederken, kriz dönemlerinde **%200 seviyelerine** tırmanmaktadır.
- **S&P 500 Oynaklığı**: Ortalama %10 – %20 bandındadır (Mart 2020 COVID-19 kriz şokunda tepe noktası %85 olmuştur).
- **Sonuç**: Bitcoin, S&P 500'e kıyasla ortalama **3.29 kat daha yüksek oynaklığa** sahiptir.

---

## 4. İstatistiksel Dağılım ve Varyans Rejimleri

Finansal getirilerin normal (Gauss) dağılıma uyup uymadığını ve aşırı uç değer (*fat-tail*) risklerini saptamak için dağılımın 3. ve 4. momentleri hesaplanmış ve yoğunluk grafikleri (KDE) çizdirilmiştir:

```python
# Dağılım Momentlerinin Hesaplanması (Çarpıklık ve Basıklık)
sp500_clean = sp500_returns.dropna()
btc_clean = btc_returns.dropna()

print(f"S&P 500 Skewness: {skew(sp500_clean):.4f}, Excess Kurtosis: {kurtosis(sp500_clean):.4f}")
print(f"Bitcoin Skewness: {skew(btc_clean):.4f}, Excess Kurtosis: {kurtosis(btc_clean):.4f}")

# Histogram ve KDE Yoğunluk Grafiği
fig, axes = plt.subplots(1, 2, figsize=(14, 5))
sns.histplot(sp500_clean, kde=True, ax=axes[0], color='#1f77b4', bins=60, stat="density")
axes[0].set_title("S&P 500 Log Getiri Dağılımı", fontweight='bold')

sns.histplot(btc_clean, kde=True, ax=axes[1], color='#ff7f0e', bins=60, stat="density")
axes[1].set_title("BTC-USD Log Getiri Dağılımı", fontweight='bold')
plt.tight_layout()
plt.show()
```

![Log Getiri Dağılımları (Histogram ve KDE)](../../../../../docs/diagrams/amplify-core/01_yahoo_returns_distribution.png)

### Dağılım Metrikleri Özeti:
| Varlık | Çarpıklık (Skewness) | Aşırı Basıklık (Excess Kurtosis) | İstatistiksel Anlamı |
|---|---|---|---|
| **S&P 500** | -0.065 | **11.896** | Nötr simetri, aşırı kalın kuyruk (ani çöküş şokları) |
| **Bitcoin** | **-0.714** | **11.483** | Belirgin sol çarpıklık (sert düşüş eğilimi) ve aşırı kalın kuyruk |

### İstatistiksel Dağılımın Sistem Mimarisine Etkileri
Standart bir Gauss dağılımında basıklık 0 civarındadır. Burada her iki varlığın da basıklığı **11'in üzerindedir (Leptokurtik / Fat-tailed)**. Bu ampirik bulgu, finansal piyasalarda "sabit varyans" varsayımının kesinlikle geçersiz olduğunu kanıtlar. Amplify Core'un **İş Paketi 2 (İP 2)** kapsamında geliştireceği **Varyans İstikrarı ($V_i$) - Levene Testi** ve **Dinamik Temel Kalibrasyon (Dynamic Baseline Calibration)** mekanizmalarının rasyoneli bu istatistiksel ispata dayanmaktadır.

---

## 5. Çıktı Veri Formatları ve Saklama Mimarisi

İş Paketi 1 (İP 1) kapsamında temizlenen ve hazırlanan veriler, mimarinin sonraki aşamalarında kullanılmak üzere `data/processed/yahoo/` klasöründe iki standart formatta saklanmıştır:

```python
# 1. Long Format (Kayıt bazlı 7 DRS göstergesinin hesaplanması için)
df_clean.to_csv('data/processed/yahoo/yahoo_finance_clean_long.csv', index=False)

# 2. Pivot Format (Zaman serisi hizalaması ve rejim testleri için)
df_pivot = df_clean.pivot(index='date', columns='symbol', values='close')
df_pivot.to_csv('data/processed/yahoo/yahoo_finance_clean_pivot.csv')
```

| Veri Dosyası | Boyut (Satır x Sütun) | Kullanım Amacı |
|---|---|---|
| `yahoo_finance_clean_long.csv` | 6.822 x 8 | Tekil kayıt bazında 7 DRS göstergesi (SNR, Missingness, CUSUM drift vb.) hesaplama motoru için |
| `yahoo_finance_clean_pivot.csv` | 4.113 x 2 (`date` indeksli) | Yönlendirme Motoru (Routing Engine) ve Model Kıyaslama (Benchmark) testlerinde tarihsel varlık hizalaması için |

---

→ [Bir Sonraki Aşama: UJIIndoorLoc Keşifsel Veri Analizi](tr/projects/systems/amplify-core/analysis-and-interim-outputs/ujiindoorloc-eda.md)
