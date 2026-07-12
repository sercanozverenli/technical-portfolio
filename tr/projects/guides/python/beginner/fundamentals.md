# Python Temelleri

Bu sayfada Python programlamaya yeni başlayanlar için temel kavramları doğal bir anlatımla öğreneceksiniz. Her bölümde önce hangi kavramın ne anlama geldiğini anlatıyorum, sonra o kavramla ilgili kısa ve açıklayıcı Python örnekleri gösteriyorum.

Bu sayfada öğrenecekleriniz
- Sayı tipleri: int ve float arasındaki farklar, kısıtlar ve dönüşümler.
- Operatörler: aritmetik operatörler, üs alma, mod ve bölüm davranışları.
- Girdi ve çıktı: input(), print(), f-string kullanımı ve kullanıcı girdilerini işleme.
- Fonksiyonlar: basit fonksiyon tanımı ve neden fonksiyon kullanmalıyız.
- Hata yakalama ve doğrulama: try/except, kullanıcı girdilerini güvenli almak.
- Pratik ipuçları.

---

## Sayı tipleri: int ve float

Python'da sayılar genellikle iki ana türde kullanılır: tam sayılar (`int`) ve ondalıklı sayılar (`float`). Temel farklar:
- `int`: negatif veya pozitif tam sayılar (örn. -3, 0, 42).
- `float`: ondalıklı sayılar (örn. 3.14, -0.5). Bellek ve doğruluk açısından sınırlamalar vardır (kayan nokta gösterimi).

Örnekler
```python
# int örneği
a = 5          # int
b = 2          # int

# float örneği
x = 2.5        # float
y = 0.1        # float
```

Bölme davranışı
- `/` operatörü her zaman float döndürür (Python 3'te).
- `//` tam bölme (floor division) yapar ve sonucu int ya da floata yuvarlanmış int olarak verir.

```python
print(5 / 2)   # 2.5
print(5 // 2)  # 2
print(-3 // 2) # -2 (floor davranışı)
```

Ondalıklı işlemlerde küçük sayısal hatalar olabilir. Çok hassas sayısal işler için `decimal` modülü veya özel kütüphaneler kullanılabilir.

---

## Operatörler

Aritmetik operatörler en çok kullanılanlardır:
- Toplama: `+`
- Çıkarma: `-`
- Çarpma: `*`
- Bölme: `/`
- Tam bölme (floor): `//`
- Mod (kalan): `%`
- Üs alma: `**`

Öncelik ve parantezler
- `**` en yüksek önceliğe sahiptir.
- Parantezler `()` ile hesaplama sırasını değiştirebilirsiniz.

Küçük örnekler
```python
print(2 + 3 * 4)    # 14, çarpma toplamadan önce gelir
print((2 + 3) * 4)  # 20, parantezle sırayı değiştirdik
print(3 ** 2)       # 9, üs alma
print(10 % 3)       # 1, mod işlemi
```

Operatörleri öğretilen bir konunun merkezine koymak için somut bir örnek: hipotenüs hesaplama. Burada üs alma ve karekökü nasıl kullanacağımızı göreceksiniz.

### Örnek: Hipotenüs hesaplama (aritmetik operatörlerin uygulaması)
Aşağıdaki örnekte önce mantığını anlatarak ilerliyoruz: iki dik kenarın karelerini al, topla, sonra karekök al.

```python
# Hipotenüs hesaplama: kullanıcıdan iki kenar al, basit doğrulama yap, sonucu göster

from math import sqrt  # okunurluk için math.sqrt kullanıyoruz

try:
    a = float(input("Birinci dik kenarın uzunluğunu girin: "))
    b = float(input("İkinci dik kenarın uzunluğunu girin: "))
except ValueError:
    print("Geçersiz giriş: lütfen sayısal bir değer girin.")
else:
    if a <= 0 or b <= 0:
        print("Uzunluklar pozitif olmalıdır.")
    else:
        # kareleri hesapla
        sum_sq = a ** 2 + b ** 2
        # karekök al (iki seçenek: **0.5 veya math.sqrt)
        c = sqrt(sum_sq)
        print(f"Hesaplanan hipotenüs: {c}")
```

Bu örnekte:
- `float()` ile kullanıcı girişini ondalıklı sayıya çeviriyoruz.
- `**` ile üs alma, `sqrt()` ile karekök alma yapıyoruz.
- `try/except` ve `if` ile basit doğrulama gösterildi.

---

## Girdi ve çıktı (input / print)

Kullanıcı etkileşimi için:
- `input(prompt)` kullanılarak kullanıcıdan metin alınır; her zaman string döner.
- Alınan string'i sayıya çevirmek için `int()` veya `float()` kullanılır.
- `print()` ile ekrana çıktı veririz; formatlama için f-string (Python 3.6+) tercih edilir.

Örnek: basit bir toplama programı
```python
try:
    s1 = float(input("Bir sayı girin: "))
    s2 = float(input("Bir sayı daha girin: "))
except ValueError:
    print("Geçersiz giriş: sayı giriniz.")
else:
    toplam = s1 + s2
    print(f"Girdiğiniz sayıların toplamı: {toplam}")
```

f-string kullanımı
```python
isim = "Ayşe"
puan = 93.4567
print(f"{isim} adlı öğrencinin notu: {puan:.2f}")  # .2f ile ondalık formatlama
```

---

## Fonksiyonlar: neden ve nasıl kullanılır?

Fonksiyonlar, kodu parçalara ayırıp tekrar kullanılabilir hale getirir. `def` anahtar kelimesiyle tanımlanır; girdiler parametrelerle, çıktı `return` ile döndürülür.

Örnek: basit bir dönüşüm fonksiyonu
```python
GALLON_TO_LITERS = 3.78541

def gallons_to_liters(gallons):
    """Galon cinsinden girilen değeri litreye çevirir."""
    return gallons * GALLON_TO_LITERS

# kullanım
print(gallons_to_liters(2))  # 2 galon kaç litre?
```

Fonksiyonların avantajları:
- Tekrar kullanım: aynı mantığı birçok yerde çağırabilirsiniz.
- Okunabilirlik: ana program akışı daha temiz olur.
- Test edilebilirlik: fonksiyonları ayrı ayrı test etmek kolaydır.

---

## Hata yakalama ve kullanıcı doğrulama

Kullanıcı girdileri dış dünya kaynaklıdır; her zaman hatalı veya beklenmedik veri gelebilir. `try/except` ile hataları yakalayın, gerekli yerde kullanıcıyı yeniden isteyin.

Tekrar isteme örneği
```python
def read_positive_float(prompt):
    while True:
        try:
            value = float(input(prompt))
        except ValueError:
            print("Geçersiz giriş. Lütfen sayısal bir değer girin.")
            continue
        if value <= 0:
            print("Pozitif bir değer girin.")
            continue
        return value

# kullanım
a = read_positive_float("Birinci dik kenarı girin: ")
b = read_positive_float("İkinci dik kenarı girin: ")
```

Bu yaklaşım, programa hatalı giriş nedeniyle beklenmedik sonlar gelmesini önler ve kullanıcı deneyimini iyileştirir.

---

## İpuçları ve dikkate alınması gerekenler

- Değişken isimleri: anlamlı isimler kullanın (`a`, `b` gibi kısa isimler örneklerde olur; gerçek projede `base`, `height` gibi isimler tercih edin).
- Ondalık hassasiyeti: `float` ile küçük kayan nokta hataları olabilir; finansal hesaplar için `decimal` kullanmayı düşünün.
- Modül kullanımı: matematiksel işlemler için `math` modülü faydalıdır (`math.sqrt`, `math.pi`, vb.).
- Performans: basit aritmetik çoğu durumda hızlıdır; büyük veri ve tekrarlayan işlemler için algoritma seçimine dikkat edin.
