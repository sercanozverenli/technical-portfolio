# Python ile Veri Analizi: Monte Carlo Simülasyonu ve Rastgele Yürüyüş

Bu bölümde, veri bilimi ve istatistikte sıkça kullanılan **Monte Carlo Simülasyonu** yöntemini kullanarak bir olasılık problemini çözeceğiz. Gerçek hayattaki belirsizlikleri bilgisayar ortamında binlerce kez taklit ederek (simüle ederek) kesin sonuçlara nasıl ulaşacağımızı göreceğiz.

### Projenin Senaryosu: Empire State Binası Oyunu
Bir arkadaşınızla iddiaya girdiğinizi hayal edin. Empire State binasının basamaklarında duruyorsunuz ve bir zar atacaksınız:
- Zar **1 veya 2** gelirse: 1 basamak aşağı ineceksiniz (0'ın altına inmek yok).
- Zar **3, 4 veya 5** gelirse: 1 basamak yukarı çıkacaksınız.
- Zar **6** gelirse: Zarı bir kez daha atıp gelen sayı kadar basamak yukarı çıkacaksınız.
- Ekstra Sakarlık Durumu: Her adımda **%0.1 (0.001)** ihtimalle merdivenlerden ayağınız kayabilir ve en alt basamağa (0'a) düşebilirsiniz.

Toplamda **100 zar atışı** yapacaksınız. Soru şu: **Bu oyunun sonunda 60. basamağa veya daha üstüne çıkma olasılığınız nedir?**

Bu olasılığı matematiksel olarak kağıt üzerinde hesaplamak sakarlık faktörü yüzünden çok zordur. Ancak bilgisayara bu oyunu **500 kez arka arkaya oynatarak** sonucu rahatça bulabiliriz.

---

## 1. numpy ve matplotlib ile Veriyi Modelleme

Bu simülasyonu yapabilmek için Python'ın matematik ve veri analizi kütüphanesi olan `numpy` ile grafik çizim kütüphanesi olan `matplotlib`'i kullanacağız.

Aşağıdaki kod bloğunda 500 farklı oyunun (rastgele yürüyüşün) tüm adımlarını hesaplıyor, ardından veriyi analiz etmek için matrisin tersini (transpozunu) alarak son ulaşılan basamakları tespit ediyoruz.

```python
import numpy as np
import matplotlib.pyplot as plt

# Her çalıştırmada aynı rastgele sayıların üretilmesi için sabit bir başlangıç (seed) noktası seçiyoruz
np.random.seed(123)

# 500 farklı simülasyonun (rastgele yürüyüşün) listesi
tum_yuruyusler = []

for i in range(500):
    rastgele_yuruyus = [0]  # Her oyun 0. basamaktan başlar
    for x in range(100):    # 100 kez zar atılacak
        mevcut_basamak = rastgele_yuruyus[-1]
        zar = np.random.randint(1, 7)  # 1 ile 6 arasında rastgele tamsayı
        
        if zar <= 2:
            # 1 veya 2 gelirse aşağı in ama 0'ın altına düşme
            mevcut_basamak = max(0, mevcut_basamak - 1)
        elif zar <= 5:
            # 3, 4 veya 5 gelirse 1 yukarı çık
            mevcut_basamak = mevcut_basamak + 1
        else:
            # 6 gelirse tekrar zar at ve gelen sayı kadar yukarı çık
            mevcut_basamak = mevcut_basamak + np.random.randint(1, 7)
        
        # Sakarlık faktörü: %0.1 ihtimalle yere kapaklanma ve 0'a dönme
        if np.random.rand() <= 0.001:
            mevcut_basamak = 0
            
        rastgele_yuruyus.append(mevcut_basamak)
    tum_yuruyusler.append(rastgele_yuruyus)

# Veriyi daha rahat analiz etmek için numpy dizisine çevirip transpozunu (tersini) alıyoruz
np_yuruyus_matrisi = np.transpose(np.array(tum_yuruyusler))

# Matrisin en son satırını seçiyoruz; bu bize 500 oyunun her birinin bittiği basamağı verir
son_basamaklar = np_yuruyus_matrisi[-1, :]
```

---

## 2. Veri Görselleştirmesi: Neden Histogram Grafiği?

Elimizde 500 oyunun sonunda ulaşılan 500 adet bitiş basamağı var. Bu sayıları düz bir liste halinde okumak bize bir şey ifade etmez. Verinin nasıl dağıldığını görmek için bir grafik çizmeliyiz.

**Neden Sütun (Bar) Grafiği Değil de Histogram?**
- **Sütun Grafikleri:** Birbirinden bağımsız kategorileri karşılaştırmak için kullanılır (Örn: Arabalar, Meyveler, Şehirler).
- **Histogram Grafikleri:** Sürekli veya sayısal verilerin hangi aralıklarda yoğunlaştığını, yani frekans dağılımını görmek için kullanılır. Biz de bitiş basamaklarının hangi sayılarda yoğunlaştığını görmek istediğimiz için histogram kullanıyoruz.

```python
# Bitiş basamaklarının histogram grafiğini çizelim
plt.hist(son_basamaklar, bins=10, edgecolor='black')
plt.title('Rastgele Yürüyüş Simülasyonu: Bitiş Basamaklarının Dağılımı')
plt.xlabel('Ulaşılan Son Basamak Numarası')
plt.ylabel('Frekans (Kaç Kez Gerçekleşti)')
plt.show()
```

---

## 3. Olasılık Hesabı ve Sonuç

Simülasyonumuz tamamlandığına göre artık asıl sorumuzun cevabını hesaplayabiliriz: **60. basamağa veya daha üstüne çıkma olasılığımız nedir?**

Numpy ile `son_basamaklar >= 60` sorgusu yaptığımızda, şartı sağlayanlar için `True` (1), sağlamayanlar için `False` (0) değerlerinden oluşan bir dizi elde ederiz. Bu dizinin ortalamasını (`np.mean`) aldığımızda, doğrudan aradığımız olasılık değerine ulaşmış oluruz.

```python
# 60. basamağa veya daha yukarısına ulaşma olasılığını hesaplama
olasilik = np.mean(son_basamaklar >= 60)

print("60. basamağa ulaşma olasılığınız: %" + str(olasilik * 100))
# Yaklaşık Çıktı: %78.4
```

### Özet Çıkarım
Yaptığımız bu Monte Carlo simülasyonu sonucunda, merdivenlerden düşme riskimize ve zarın şans faktörüne rağmen bu oyunda 60. basamağa ulaşma şansımızın **yaklaşık %78.4** gibi yüksek bir oran olduğunu bilgisayar yardımıyla kanıtlamış olduk.
