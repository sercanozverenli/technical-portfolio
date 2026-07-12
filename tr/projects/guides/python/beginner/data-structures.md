# Veri Yapıları (List, Tuple, Set, Dict)

Bu sayfada Python'da en sık kullanılan temel veri yapılarını (liste, tuple, set, sözlük) öğreneceksiniz. Her yapı için hangi durumlarda tercih edildiğini, nasıl oluşturulup kullanıldığını ve pratik örnekleri doğal Türkçe ile anlatıyorum. Anlatım önce konu, sonra ilgili örnek kod şeklinde ilerlerir.

Bu sayfada öğrenecekleriniz
- Listeler: dinamik koleksiyonlar, ekleme/çıkarma, dilimleme (slicing)
- Tuple: sabit (immutable) diziler ve kullanım alanları
- Set: benzersiz elemanlar, kümelerle işlemler (kesişim, birleşim, fark)
- Dict (sözlük): anahtar-değer depolama, get, items, keys, values ve kullanım örüntüleri

---

## Listeler (list)

Listeler aynı türde veya farklı türde verileri saklayabilen, eleman ekleyip çıkarabileceğiniz, sıralı (ordered) koleksiyonlardır. Yeni eleman eklemek, var olanı değiştirmek ve dilimleme yapmak listelerin temel gücüdür.

Oluşturma ve temel işlemler
```python
# Liste oluşturma
meyveler = ['elma', 'armut', 'muz']

# Eleman ekleme
meyveler.append('kiraz')    # sona ekler
meyveler.insert(1, 'çilek') # belirtilen indekse ekler

# Eleman çıkarma
meyveler.remove('armut')    # değere göre çıkarır (ilk bulunan)
son = meyveler.pop()        # sondan çıkarıp döndürür

# Erişim ve dilimleme
ilk = meyveler[0]
son_iki = meyveler[-2:]

print(meyveler)
```

Dilimleme (slicing)
- `liste[start:stop:step]` ile alt liste alabilirsiniz. `stop` dahil değildir.

```python
nums = [0,1,2,3,4,5]
print(nums[1:4])   # [1,2,3]
print(nums[:3])    # [0,1,2]
print(nums[::2])   # [0,2,4]
```

Notlar
- Listeler mutable (değiştirilebilir) olduğu için kopyalama yaparken dikkat edin: `b = a` ataması referans kopyası yapar; gerçek kopya için `a.copy()` veya `list(a)` kullanın.

---

## Tuple (tuple)

Tuple, listelere benzer ama immutable (değiştirilemez) bir veri yapısıdır. Sabit koleksiyonlar için, fonksiyonlardan birden fazla değer döndürürken veya dictionary anahtarı olarak kullanılacak veri tutarken tuple tercih edilir.

Örnek
```python
koordinat = (10.0, 20.0)   # değiştirilemez
# koordinat[0] = 5  # TypeError: 'tuple' object does not support item assignment

# Tuple açma (unpacking)
x, y = koordinat
```

Notlar
- Tuple'lar immutable olduğu için thread-safe küçük veri paketleri veya sabit yapılar için uygundur.

---

## Set (kümeler)

Setler benzersiz elemanlar kümesi tutar. Aynı elemandan yalnızca bir tane saklanır; bu özellik benzersizleştirme işlemlerinde ve küme teorisi işlemlerinde (kesişim, birleşim, fark) kullanışlıdır.

Oluşturma ve temel işlemler
```python
s = {1, 2, 3}
s.add(4)
s.discard(2)   # elemanı çıkarır; yoksa hata vermez

# Küme işlemleri
A = {1,2,3}
B = {3,4,5}
print(A & B)   # kesişim {3}
print(A | B)   # birleşim {1,2,3,4,5}
print(A - B)   # fark {1,2}
```

Set ile benzersizleştirme
```python
items = [1,2,2,3,3,3]
unique = set(items)   # {1,2,3}
```

Notlar
- Set öğeleri sırasızdır (unordered) ve indeksleme yoktur.
- Set içindeki öğeler hashable olmalıdır (ör. listeler set içinde olamaz).

---

## Sözlükler (dict)

Sözlükler anahtar-değer çiftlerini saklar. Hızlı anahtar tabanlı erişim gerektiğinde dict kullanılır. Anahtarlar hashable olmalı (genelde string veya sayılar tercih edilir).

Temel kullanım
```python
person = {
    'isim': 'Ali',
    'yas': 30,
}

# Değer okuma
print(person['isim'])         # KeyError verir eğer anahtar yoksa
print(person.get('meslek'))  # None döner, anahtar yoksa hata vermez

# Değer güncelleme / ekleme
person['yas'] = 31
person['meslek'] = 'mühendis'

# Iterasyon
for key, value in person.items():
    print(key, value)
```

Sıklıkla kullanılan yöntemler
- `dict.keys()`, `dict.values()`, `dict.items()`
- `dict.get(key, default)` — varsayılan değer sağlamak için kullanışlıdır
- `in` operatörü ile anahtar kontrolü: `if 'isim' in person:`

---

## Örnek: Beatles listesi oluşturma (liste işlemleri ile)

Aşağıda bir listeye ad ekleme ve eklenenleri yazdırma örneği var. Bu örnek, `append`, `insert`, `pop` gibi liste operasyonlarını gösterir.

```python
beatles = []
# Başlangıç üyelerini ekleyelim
beatles.append('John Lennon')
beatles.append('Paul McCartney')
beatles.append('George Harrison')

# Son üyeyi ekle
beatles.insert(1, 'Ringo Starr')  # 1. indekse Ringo'yu ekledik

print('Beatles üyeleri:')
for member in beatles:
    print('-', member)
```

---

## Örnek: Benzersiz eleman filtresi (set ile)

Listenizde tekrar eden öğeler varsa bunları hızlıca benzersiz hale getirmek için set kullanabilirsiniz.

```python
items = [1, 2, 2, 4, 3, 3, 1]
unique_list = list(set(items))
print(unique_list)  # sırası belirsiz olabilir
```

Sıralı benzersizlik isterseniz, bir `seen` set'i ile orijinal sırayı koruyarak filtreleyebilirsiniz:
```python
def unique_preserve_order(seq):
    seen = set()
    result = []
    for x in seq:
        if x not in seen:
            seen.add(x)
            result.append(x)
    return result

print(unique_preserve_order(items))
```

---

## İpuçları ve dikkat edilmesi gerekenler

- Hangi veri yapısını seçeceğinize veri erişim ve güncelleme senaryonuza göre karar verin: hızlı arama için `dict` ve `set`, sıralı erişim için `list`.
- Büyük veri ile çalışırken gereksiz kopyalamalardan kaçının (ör. `list.copy()` veya slice gerektiğinde kullanın).
- Immutable veri (tuple) küçük veri paketleri ve hashable ihtiyaçlar için uygundur.

