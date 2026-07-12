# Python'da Algoritma Mantığı, Veri Doğrulama ve String İşleme

Bu rehber orta seviye (intermediate) okuyucu için hazırlanmıştır. Amacımız, basit araçların arkasındaki algoritmik mantığı, güvenli giriş/çıkış (input) örüntülerini ve metin temelli işlemleri derinlemesine ele almaktır. Her bölümde önce kavramsal açıklama yapıyorum, sonra gerekli yerlerde kod snippet'leri veriyorum; bir fonksiyonun tamamı açıklama için kritikse o fonksiyonun tamamını ekliyorum.

Bu dosyayı okuduktan sonra:
- Manuel string ayırma (split) algoritmasını ve hazır metodlarla farklarını anlayacaksınız.
- Palindrome, anagram ve basit arama gibi metin problemlerini uygulayacaksınız.
- Kullanıcıdan güvenli sayı okuma (defensive input) için tekrar eden örüntüleri öğreneceksiniz.
- 2B listeler (matris) üzerinde doğrulama ve küme kullanarak benzersizlik kontrollerini (ör. Sudoku doğrulayıcı) uygulayacaksınız.

---

## 1. String İşleme ve Arka Plan Algoritmaları

Neden manuel bir `split` yazalım? Python'un `str.split()` fonksiyonu çoğu durumda yeterli olsa da, eğitim amaçlı edge-case'leri ve karakter seviye işleme ihtiyaçlarını görmek için basit bir implementasyon faydalıdır. Aşağıdaki `mysplit` örneği boşlukları ayırarak kelimeleri toplar, birden fazla boşluk veya baş/son boşluk durumlarını güvenli şekilde yönetir.

```python
# mysplit: basit bir manuel split implementasyonu

def mysplit(strng):
    """Split the given string into words without using str.split()."""
    mylist = []
    word = ""

    # if input is empty or only spaces, return empty list
    if not strng or strng.isspace():
        return mylist

    for i in strng:
        if i != " ":
            # accumulate non-space characters
            word += i
        else:
            # on space, if we have a word, append and reset
            if word != "":
                mylist.append(word)
                word = ""

    # append last word if present
    if word != "":
        mylist.append(word)

    return mylist

# test cases
print(mysplit("To be or not to be, this is a question"))
print(mysplit("   "))            # []
print(mysplit("abc  "))
```

Açıklamalar:
- Yöntem O(n) zaman karmaşıklığına sahiptir (n = karakter sayısı).
- `str.split()` çok daha esnektir (ör. farklı ayırıcılar) ve C implementasyonu olduğu için genellikle daha hızlıdır; ancak manuel yöntem karakter tabanlı mantığı anlamak için öğreticidir.
- Daha karmaşık ayırıcılar veya desenler için `re.split()` kullanın (ör. birden fazla ayırıcı tipi).

İyileştirme ve kenar durumları:
- Unicode boşluk karakterleri veya tab/yenisatır gibi farklı beyaz boşlukları desteklemek isterseniz `str.isspace()` ile kontrol edebilirsiniz.
- Çok büyük metinlerde bellek açısından bir generator yaklaşımı (`yield`) tercih edilebilir.

---

## 2. Karakter ve Metin Tabanlı Problemler

Burada sıkça karşılaşılan üç problemi kısaca ele alıyoruz: palindrome, anagram ve "find a word".

Palindrome kontrolü (sade yöntem):

```python
def is_palindrome(s):
    s2 = ''.join(ch.lower() for ch in s if ch.isalnum())  # normalize: harf/rakam
    return s2 == s2[::-1]

print(is_palindrome("A man, a plan, a canal: Panama"))  # True
```

Notlar:
- Normalize ederken noktalama işaretleri ve boşlukları atıyoruz; büyük/küçük harf ayrımını kaldırıyoruz.
- Dilin özelliklerine göre aksanlı karakterler için ek normalizasyon gerekebilir.

Anagram kontrolü (iki yol):
- Sıralama yöntemi (basit ve okunaklı): O(n log n)
- Frekans sayımı (sözlük/set ile): O(n)

Örnek (frekans sayımı):

```python
from collections import Counter

def are_anagrams(a, b):
    return Counter(a.replace(' ', '').lower()) == Counter(b.replace(' ', '').lower())

print(are_anagrams('listen', 'silent'))  # True
```

Kelime arama (basit): `str.find()` veya `in` operatörü yeterlidir. Karmaşık desen aramaları için `re.search()` kullanın.

---

## 3. İstisna Yönetimi ve Savunmacı Programlama (Defensive Programming)

Kullanıcı girdileri her zaman güvenilmezdir. Aşağıdaki `read_int` fonksiyonu, belirli bir aralıkta güvenli bir tamsayı okumak için tipik bir kalıptır. Fonksiyon kullanıcıyı tekrar ister, hatalı girişlerde açıklayıcı hata mesajı verir.

```python
# Safe int input: belirli aralıkta tamsayı okur

def read_int(prompt, min, max):
    """Safely read an integer from user within [min, max].

    Returns an int entered by the user within the requested range.
    """

    # Guidance for user and edge cases handled inside
    ok = False
    while not ok:
        try:
            value = int(input(prompt))
            ok = True
        except ValueError:
            print("Error: wrong input")

        if ok:
            # check range
            ok = value >= min and value <= max

        if not ok:
            print(f"Error: the value is not within permitted range ({min}..{max}).")

    return value

# example usage
v = read_int("Enter a number from -10 to 10: ", -10, 10)
print("The number is:", v)
```

İyi uygulamalar:
- Fonksiyonlara docstring ekleyin ve beklenen davranışı açıkça belirtin.
- Hatalı girişlerde kullanıcıya ne yapması gerektiğini söyleyin; sonsuz döngü riskini azaltmak için yeniden deneme sayısı (retry limit) ekleyebilirsiniz.
- GUI/servis ortamlarında input yerine parametre doğrulamasını çağrı sınırında yapın.

---

## 4. İki Boyutlu Listeler (Matrisler) ve Küme (set) Teorisi — Sudoku Doğrulayıcı

Sudoku doğrulayıcı, 2B liste işlemleri ve küme tabanlı benzersizlik kontrolleri için ideal bir örnektir. Temel fikir:
- Her satırda 1..9 rakamları benzersiz olmalı.
- Her sütunda 1..9 benzersiz olmalı.
- Her 3x3 alt-karede 1..9 benzersiz olmalı.

Aşağıda doğrulamaya yönelik yardımcı fonksiyonları ve ana kontrol fonksiyonunu veriyorum (repo'daki implementasyondan çıkarım):

```python
def checkset(digs):
    """Return True if the given iterable contains exactly the digits 1..9."""
    return set(digs) == set("123456789")

# sudoku validator: temel yaklaşım

def sudoku_validator(grid):
    """
    grid: list of rows, each row is a string or iterable of characters/digits.
    Returns True if the sudoku constraints are satisfied for given rows.
    """
    rows = []

    # input rows -> normalize and collect
    for i in range(9):
        row = input(f"Please enter a nine-digit number for row {i+1}: ")
        # validation omitted here; assume row contains exactly 9 digits
        rows.append(row)

    # check rows
    for row in rows:
        if not checkset(row):
            print("No")
            return False

    # check columns
    for i in range(9):
        col = [row[i] for row in rows]
        if not checkset(col):
            print("No")
            return False

    # check 3x3 sub-squares
    for r in range(0, 9, 3):
        for c in range(0, 9, 3):
            sqr = []
            for i in range(3):
                for j in range(3):
                    sqr.append(rows[r+i][c+j])
            if not checkset(sqr):
                print("No")
                return False

    print("Yes")
    return True
```

Notlar ve iyileştirmeler:
- `checkset` fonksiyonu `O(k)` zaman alır (`k=9` sabit), toplam doğrulama `O(81)` yani sabit zaman gibidir; pratikte çok hızlıdır.
- Girdi validasyonu önemlidir: kullanıcıdan gelen stringlerin uzunluğu ve karakter seti kontrol edilmeli.
- Alternatif: Sudoku doğrulamasını tek bir geçişte yapacak mantık kurulabilir (ör. satır/sütun/alt-kareleri aynı anda güncelleyen kümeler), ama okunabilirlik/karmaşıklık dengesi göz önünde bulundurulmalı.

---

## 5. Birleştirme, Test ve Karmaşıklık Notları

- Algoritmaların çoğu bu modül içinde O(n) veya O(n log n) karmaşıklığındadır; Sudoku gibi 9x9 sabit boyutlu problemlerde karmaşıklık sabit sayıdır.
- Her fonksiyon için birim testler yazın (`pytest` ile) — ör: `mysplit` için boş, tek boşluk, çoklu boşluk, baş/son boşluk testleri.
- Performans ölçümleri için `time.perf_counter()` kullanarak mikro-benchmark'lar yapabilirsiniz.

---

## Ödev/İleri Adımlar

- `mysplit` fonksiyonunu `yield` kullanan bir generator'a dönüştürün; büyük metinler için bellek kullanımını azaltın.
- Sudoku doğrulayıcıyı dosyadan okuma/JSON input ile çalışacak şekilde genişletin ve bir test seti oluşturun.
- `read_int` fonksiyonunu bir `retry` parametresi ile genişletin (maksimum deneme sayısı) ve hata türlerine göre farklı mesaj verin.

---

Dosyanın içeriğini onaylarsanız, bu dosyayı tr/projects/guides/python/intermediate/algoritmalar.md olarak repoya ekleyip commit edeceğim; ardından `oopyapilar.md` dosyasına geçeriz.