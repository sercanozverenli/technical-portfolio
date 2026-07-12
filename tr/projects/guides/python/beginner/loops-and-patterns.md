# Döngüler ve Yinelemeler

Bu sayfada Python'da döngülerin nasıl kullanıldığını, `for` ve `while` döngülerinin farklarını, sık kullanılan kalıpları ve döngülerle veri işleme örneklerini doğal bir anlatımla öğreneceksiniz. Her bölümde önce kavramı açıklıyor, sonra açıklamayı pekiştiren örnek kodlar veriyorum.

Bu sayfada öğrenecekleriniz
- `for` döngüsünün kullanımı, `range()` ile sayma
- Diziler (listeler, stringler, tuple) üzerinde yineleme
- `while` döngüsü ve durma koşulları
- `break`, `continue`, `else` ile döngü kontrolü
- `enumerate()` ve `zip()` ile indeks ve paralel yineleme
- List comprehension (liste üretimleri) ile pratik ve okunabilir kod yazımı
- Yaygın örnekler: karakter sayma, eleman filtreleme, desen çizimi, Collatz örneği

---

## `for` döngüsü: temel kullanım

`for` döngüsü bir koleksiyonun öğeleri üzerinde sırasıyla işlem yapmak için kullanılır. En basit haliyle:
```python
for item in collection:
    # item ile yapılacak işler
```

Örnek: bir listenin elemanlarını yazdırma
```python
meyveler = ['elma', 'armut', 'muz']
for meyve in meyveler:
    print(meyve)
```

`range()` ile sayma
`range(n)` 0'dan başlayıp `n-1`'e kadar sayan bir nesne üretir. `range(start, stop, step)` ile adımları değiştirebilirsiniz.
```python
for i in range(5):         # 0,1,2,3,4
    print(i)
for i in range(1, 10, 2):  # 1,3,5,7,9
    print(i)
```

---

## Diziler üzerinde yineleme: string, list, tuple

Stringler de yine iterable (yinelenebilir) nesnelerdir; karakter karakter dolaşabilirsiniz.
```python
s = 'merhaba'
for ch in s:
    print(ch)
```

Liste üzerinde indeksle birlikte gezinmek için `enumerate()` kullanın:
```python
liste = ['a', 'b', 'c']
for i, val in enumerate(liste, start=1):
    print(i, val)  # 1 a, 2 b, 3 c
```

Paralel yineleme için `zip()` kullanabilirsiniz:
```python
adlar = ['Ali', 'Ayşe']
notlar = [80, 95]
for isim, notu in zip(adlar, notlar):
    print(f"{isim}: {notu}")
```

---

## `while` döngüsü: koşul tabanlı yineleme

`while` döngüsü belirli bir koşul doğru olduğu sürece çalışır. Genellikle sayma, koşula bağlı devam etme veya belirli bir duruma gelene kadar tekrarlama gerektiren durumlarda kullanılır.

Örnek: 1'den 5'e kadar sayma
```python
i = 1
while i <= 5:
    print(i)
    i += 1
```

`while` döngüsünde koşulun bir noktada `False` olacağını garanti etmek önemlidir; aksi halde sonsuz döngü oluşur.

---

## Döngü kontrolü: `break`, `continue`, `else`

- `break`: döngüyü hemen sonlandırır.
- `continue`: o iterasyonu atlayıp döngünün bir sonraki adımına geçer.
- `for/while ... else`: döngü normal şekilde sona ererse `else` bloğu çalışır; `break` ile çıkılırsa `else` çalışmaz.

Örnek: sayı bulunana kadar arama
```python
nums = [3, 7, 10, 2]
for n in nums:
    if n == 10:
        print('Bulundu:', n)
        break
else:
    print('Bulunamadı')
```

---

## Liste üretimleri (list comprehensions)

List comprehension, kısa ve okunaklı bir şekilde yeni listeler oluşturmanızı sağlar. Temel biçimi:
```python
[<ifade> for <eleman> in <iterable> if <koşul>]
```

Örnekler
```python
# 1'den 10'a kadar çift sayıların listesi
ciftler = [i for i in range(1, 11) if i % 2 == 0]

# Bir listenin elemanlarının kareleri
kareler = [x**2 for x in [1, 2, 3, 4]]
```

List comprehension genellikle döngü + ekleme işlemini tek satırda yazmanıza olanak verir; okunurluğu bozmuyorsa tercih edilir.

---

## Yaygın örnekler ve kalıplar

Aşağıda döngülerle sık kullanılan bazı pratik örnekleri bulacaksınız. Her örnek kısa açıklama ve kod içerir.

### Karakter sayma (örnek: "Mississippi" içindeki 's' sayısı)
```python
s = 'Mississippi'
count = 0
for ch in s:
    if ch.lower() == 's':
        count += 1
print('s harfi sayısı:', count)
```

Bu örnek `for` döngüsü ve sayaç kavramını gösterir.

---

### Sesli harfleri kaldırma (örnek: Remove Vowels)

Bu örnekte bir string içindeki sesli harfleri kaldırıp yeni bir string oluşturacağız.
```python
text = 'Merhaba Dünya'
vowels = 'aeıioöuüAEIİOÖUÜ'
result = ''.join([ch for ch in text if ch not in vowels])
print(result)
```

Alternatif olarak döngü ile adım adım yapabilirsiniz; list comprehension burada hem kısa hem de açıklayıcıdır.

---

### Piramit yüksekliği / desen basımı (basit örnek)

Döngülerle sıklıkla çıkan bir alıştırma, belirli bir deseni yazdırmaktır. Aşağıda bir piramit yüksekliği örneği gösterilmiştir.
```python
height = 4
for i in range(1, height + 1):
    print(' ' * (height - i) + '*' * (2 * i - 1))
```

Bu kod her satır için boşluk ve yıldız sayısını hesaplar; `range()` ve string çarpımı (`'*' * n`) kombinasyonunu gösterir.

---

### Collatz dizisi (while döngüsü örneği)

Collatz problemi: pozitif bir tam sayı ile başlayın; sayı çiftse 2 ile bölün, tekse 3n+1 yapın; 1'e ulaşana kadar tekrarlayın. Bu örnek `while` ile döngü mantığını gösterir.

```python
n = int(input('Başlangıç pozitif tam sayıyı girin: '))
steps = 0
while n != 1:
    if n % 2 == 0:
        n = n // 2
    else:
        n = 3 * n + 1
    steps += 1
print('Toplam adım:', steps)
```

Bu örnek, döngü ile durum güncelleme ve sayaç kullanımını gösterir.

---

## İpuçları ve dikkat edilmesi gerekenler

- Döngülerde gereksiz kopyalamalardan kaçının; büyük verilerle çalışırken `for` içinde gereksiz liste oluşturmamak performansı artırır.
- Sonsuz döngü riskine karşı `while` kullanırken durma koşulunu iyi tasarlayın veya bir maksimum adım sayısı koyun.
- `enumerate()` ve `zip()` kodu daha okunaklı hâle getirir; indeks ihtiyacı olan durumlarda `range(len(...))` kullanmak yerine `enumerate` tercih edin.

