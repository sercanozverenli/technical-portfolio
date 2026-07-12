# Koşullar ve Karar Yapıları

Bu sayfada Python'da koşulları nasıl yazacağınızı, karşılaştırma ve mantıksal operatörlerin nasıl çalıştığını, if/elif/else yapılarını ve pratik kullanım örneklerini öğreneceksiniz. Anlatım doğal Türkçe olacak; önce her kavramı açıklıyor, sonra ilgili ve açıklayıcı örnek kodları gösteriyorum.

Bu sayfada öğrenecekleriniz
- Karşılaştırma operatörleri (==, !=, >, <, >=, <=)
- Mantıksal operatörler (and, or, not) ve kısa devre (short-circuit) davranışı
- if / elif / else yapısı ve iyi kullanım örüntüleri
- İç içe koşullar (nested if) ve güvenli ifadeler
- İki pratik örnek: artık yıl kontrolü ve etkinlik zaman hesaplama

---

## Karşılaştırma operatörleri

Karşılaştırma operatörleri, iki değeri karşılaştırıp sonuç olarak `True` veya `False` dönerler. Yaygın olarak kullanılanlar:
- `==` : eşit mi?
- `!=` : eşit değil mi?
- `>`  : büyük mü?
- `<`  : küçük mü?
- `>=` : büyük veya eşit mi?
- `<=` : küçük veya eşit mi?

Örnekler
```python
print(5 == 5)   # True
print(5 != 3)   # True
print(4 > 7)    # False
print(3 <= 3)   # True
```

Notlar
- `==` ile atama operatörü `=` karıştırılmamalıdır. Atama `=`, karşılaştırma `==` ile yapılır.
- Karşılaştırmalar sayılar dışında string, list vb. tiplerde de çalışır (ör. `'a' < 'b'`).

---

## Mantıksal operatörler: and, or, not

Mantıksal operatörler bir veya daha fazla koşulu birleştirir:
- `and`: her iki ifade de doğruysa `True` döner.
- `or` : en az bir ifade doğruysa `True` döner.
- `not`: ifadenin tersini alır.

Kısa devre (short-circuit)
- `and` ifadesinde soldaki operand `False` ise sağ taraf değerlendirilmez (sonuç kesin `False`).
- `or` ifadesinde soldaki operand `True` ise sağ taraf değerlendirilmez (sonuç kesin `True`).

Örnekler
```python
a = 10
b = 5
print(a > 0 and b > 0)   # True, her ikisi de pozitif
print(a < 0 or b > 0)    # True, ikinci ifade doğru
print(not (a == b))      # True, a ile b eşit değil
```

Kısa devre kullanarak güvenli ifadeler yazabilirsiniz. Örneğin bir nesnenin `None` olup olmadığını kontrol edip sonra onun özelliğini okumak için:
```python
if obj is not None and obj.value > 0:
    print("Değer pozitif")
```
Burada `obj is not None` yanlışsa ikinci ifade (`obj.value`) asla çalışmaz, böylece AttributeError engellenir.

---

## if / elif / else yapısı

Python'da koşullu ifadeler şu şekilde yazılır:
```python
if koşul1:
    # koşul1 True ise burası çalışır
elif koşul2:
    # koşul2 True ise burası çalışır
else:
    # hiçbir koşul True değilse burası çalışır
```

İyi uygulamalar
- Çok sayıda `elif` gerekiyorsa, veriyi bir sözlük (`dict`) ile eşleyip daha okunabilir bir yapı oluşturmak genellikle daha iyidir.
- Basit kararlar için ternary (tek satırlık) ifade kullanılabilir:
  `x = 'pozitif' if n > 0 else 'pozitif değil'`

Örnek: puana göre harf notu
```python
def letter_grade(score):
    if score >= 90:
        return 'A'
    elif score >= 80:
        return 'B'
    elif score >= 70:
        return 'C'
    elif score >= 60:
        return 'D'
    else:
        return 'F'

print(letter_grade(85))  # B
```

---

## İç içe koşullar ve güvenlik

Bazı durumlarda bir koşulun içinde başka koşullar gerekir. Ancak iç içe if bloklarını fazla derinleştirmek kodu zor okunur hale getirir; bunun yerine fonksiyonlara bölmek veya early return (erken dönüş) kullanmak okunurluğu artırır.

Örnek: güvenli erken dönüş
```python
def process(value):
    if value is None:
        return
    if not isinstance(value, int):
        return
    # artık value güvenli bir int
    print(value * 2)
```

---

## Örnek 1 — Artık yıl kontrolü (uygulama)

Bu örnek, if/elif/else kullanımını pekiştirmek için klasik bir problemdir. Öncelikle kuralları hatırlayalım:
- Eğer yıl 400 ile tam bölünüyorsa artık yıldır.
- Yok ama 100 ile tam bölünüyorsa artık yıl değildir.
- Yok ama 4 ile tam bölünüyorsa artık yıldır.
- Aksi halde artık yıl değildir.

Açıklama: Buradaki akış, en spesifik durumdan (400) daha genel duruma (4) doğru ilerler. Bu sayede doğru karar alınır.

Kod
```python
try:
    year = int(input("Yılı girin: "))
except ValueError:
    print("Geçersiz yıl")
else:
    if year % 400 == 0:
        print("Artık yıl")
    elif year % 100 == 0:
        print("Artık yıl değil")
    elif year % 4 == 0:
        print("Artık yıl")
    else:
        print("Artık yıl değil")
```

Bu kodda önce kullanıcı girdisini güvenli bir şekilde `int`'e çeviriyoruz. Ardından en spesifik durumdan başlayarak (`% 400`) kontrolleri yapıyoruz.

---

## Örnek 2 — Etkinlik zamanı hesaplama (pratik uygulama)

Senaryo: Bir etkinlik başlangıç zamanınız var ve kullanıcıdan etkinliğin süresini dakika olarak alıp bitiş saatini hesaplayacağız. Bu örnek, sayı işlemleri ve koşullu kontrolleri bir arada kullanmayı gösterir.

Adımların mantığı:
1. Kullanıcıdan başlangıç saatini "saat:dakika" biçiminde alsın (ör. 14:20).
2. Kullanıcıdan süresi (dakika) alınsın.
3. Başlangıç saatini saat ve dakikaya ayırıp toplam dakikayı hesaplayalım.
4. Süre eklenince yeni toplam dakikayı saate çevirip 24 saat formatında gösterelim.

Kod
```python
# Başlangıç zamanını "HH:MM" formatında alır ve bitiş zamanını hesaplar

def parse_time(hhmm):
    """HH:MM biçimindeki string'i (saat, dakika) tuple'ına çevirir."""
    parts = hhmm.split(":")
    if len(parts) != 2:
        raise ValueError("Zaman HH:MM biçiminde olmalı")
    hour = int(parts[0])
    minute = int(parts[1])
    if not (0 <= hour < 24 and 0 <= minute < 60):
        raise ValueError("Geçersiz saat veya dakika")
    return hour, minute

try:
    start = input("Etkinlik başlangıç zamanı (HH:MM): ")
    duration = int(input("Süre (dakika): "))
    h, m = parse_time(start)
except ValueError as e:
    print("Geçersiz giriş:", e)
else:
    total_minutes = h * 60 + m + duration
    end_hour = (total_minutes // 60) % 24
    end_minute = total_minutes % 60
    print(f"Etkinlik bitiş zamanı: {end_hour:02d}:{end_minute:02d}")
```

Bu örnekte `parse_time` fonksiyonu ile giriş doğrulaması yapmak, kodu okunur ve tekrar kullanılabilir kılar. `//` tam bölme ve `%` mod operatörü saat/dakika dönüşümlerinde kullanıldı.
