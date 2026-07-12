# String İşleme ve Güvenli Kodlama

Bu bölümde Python ile problem çözerken en çok ihtiyaç duyacağımız şu temel konuları ele alacağız:
- Hazır fonksiyonlar kullanmadan metinleri (string) manipüle etme yöntemleri.
- Palindrome, anagram ve metin içinde kelime arama gibi klasik problemlerin çözümü.
- Programın çökmesini engelleyen "Savunmacı Programlama" (Defensive Programming) teknikleri.
- İki boyutlu listeler (matrisler) ve benzersizlik kontrolü için `set` (küme) kullanımı.

---

## 1. String İşleme ve Arka Plan Algoritmaları

Normalde Python'da bir metni kelimelerine ayırmak için hazır `str.split()` metodunu kullanırız. Peki arka planda bu işler nasıl dönüyor? Bir metodun mantığını kavramanın en iyi yolu, onu sıfırdan yazmaktır. 

Aşağıdaki `mysplit` fonksiyonu, hiçbir hazır metot kullanmadan metindeki kelimeleri bulur. Başta, sonda veya kelime aralarında birden fazla boşluk olması gibi tüm "istisnai durumları" (edge cases) başarıyla yönetir.

```python
def mysplit(strng):
    """
    Hazır split() metodunu kullanmadan metni boşluklardan ayırır.
    """
    mylist = []  # Kelimeleri toplayacağımız liste
    word = ""    # Karakterleri birleştirip kelime yapacağımız geçici değişken

    # Metin boşsa veya sadece boşluklardan oluşuyorsa direkt boş liste dön
    if not strng or strng.isspace():
        return mylist

    # Metindeki her bir karakteri tek tek inceleyelim
    for i in strng:
        if i != " ":
            # Boşluk olmayan karakterleri yan yana ekleyerek kelimeyi oluştur
            word += i
        else:
            # Boşluğa denk geldiysek ve elimizde bir kelime biriktiyse listeye ekle
            if word != "":
                mylist.append(word)
                word = ""  # Yeni kelime için hafızayı temizle

    # Döngü bittiğinde içeride kalan son kelimeyi de listeye eklemeyi unutma
    if word != "":
        mylist.append(word)

    return mylist

# Test Edelim
print(mysplit("To be or not to be, this is a question"))
print(mysplit("To be or not to be,this is a question"))
print(mysplit("   "))   # Çıktı: []
print(mysplit(" abc "))  # Çıktı: ['abc']
print(mysplit(""))      # Çıktı: []
```

### Bilmemiz Gerekenler:
*   **Performans:** Bu fonksiyon metindeki tüm karakterleri tek bir seferde taradığı için lineer, yani O(n) zaman karmaşıklığıyla çalışır.
*   **Hazır Fonksiyon Avantajı:** Gerçek projelerde elbette Python'un kendi `split()` metodunu kullanmalıyız. Çünkü arka planda C diliyle yazılmıştır ve çok daha hızlı çalışır. Ayrıca virgül veya nokta gibi farklı ayırıcıları da destekler.

---

## 2. Karakter ve Metin Tabanlı Problemler

Yazılım mülakatlarında ve algoritma yarışmalarında karşımıza en çok çıkan 4 popüler metin problemini tek bir çatı altında toplayalım. 

### 1. Palindrome (Tersten Okunuş)
Bir kelimenin veya cümlenin düzden ve tersten okunuşunun aynı olması durumudur. Büyük/küçük harfleri ve boşlukları yok sayarak kontrol etmemiz gerekir.

### 2. Anagram (Harf Değiştirme)
İki farklı kelimenin tamamen aynı harflerden oluşup oluşmadığını kontrol eder. Örneğin "listen" ve "silent" kelimeleri birbirinin anagramıdır.

### 3. Digit of Life (Yaşam Sayısı)
Doğum tarihindeki tüm rakamları tek bir basamak kalana kadar durmadan kendi arasında toplama algoritmasıdır.

### 4. Find a Word (Kelime Avı)
İlk kelimenin harflerinin, ikinci metnin içinde sırasıyla (arada başka harfler olsa bile) var olup olmadığını kontrol eder.

İşte bu 4 oyunu içeren fonksiyonlar:

```python
def palindrome():
    """Kullanıcıdan alınan metnin palindrome olup olmadığına bakar."""
    pl = input("Lütfen bir metin girin: ").lower().replace(" ", "")
    r_pl = pl[::-1]  # Metni ters çeviriyoruz

    if pl == "" or pl != r_pl:
        print("Bu bir palindrome değil.")
    else:
        print("Harika! Bu bir palindrome.")


def anagram():
    """İki metnin birbirinin anagramı olup olmadığını doğrular."""
    pl1 = input("İlk metni girin: ").lower().replace(" ", "")
    pl2 = input("İkinci metni girin: ").lower().replace(" ", "")

    # Harfleri sıraladığımızda ikisi de aynı oluyorsa anagramdır
    if sorted(pl1) != sorted(pl2) or pl1 == "":
        print("Anagram değil.")
    else:
        print("Evet, bunlar anagram!")


def digit_of_life():
    """Doğum tarihini tek basamaklı bir sayıya indirger."""
    bd = input("Doğum tarihinizi GGAAYYYY formatında girin (Örn: 15031990): ").strip()

    # Sayı tek basamaklı kalana kadar döngüyü çalıştır
    while len(bd) > 1:
        total = 0
        for i in bd:
            total += int(i)
        bd = str(total)  # Yeni toplamı stringe çevirip tekrar döngüye sok

    print("Yaşam Sayınız:", bd)


def find_word():
    """İlk kelimenin harfleri ikinci metinde sırayla var mı diye bakar."""
    word = input("Aradığınız kelime: ").upper()
    strn = input("İçinde aranacak metin: ").upper()

    found = True
    start = 0

    for i in word:
        # Harfi, kaldığımız son indeksten sonrasında arıyoruz
        pos = strn.find(i, start)
        if pos == -1:
            found = False
            break
        else:
            start = pos + 1  # Bir sonraki harfi aramak için indeksi kaydır

    if found:
        print("Kelime metnin içinde gizli!")
    else:
        print("Maalesef kelime bulunamadı.")
```

---

## 3. İstisna Yönetimi ve Savunmacı Programlama (Defensive Programming)

Kullanıcılar her zaman programımızın beklediği verileri girmezler. Tamsayı (`int`) istediğimiz bir yere harf girebilirler ya da izin verdiğimiz aralığın dışına çıkabilirler. İyi bir yazılımcı, kullanıcının hata yapabileceğini öngörerek kod yazar.

Aşağıdaki `read_int` fonksiyonu, kullanıcı doğru aralıkta geçerli bir sayı girene kadar çalışan ve hata durumunda programın çökmesini engelleyen güvenli bir kalıptır.

```python
def read_int(prompt, min, max):
    """
    Kullanıcıdan belirli bir aralıkta güvenli bir şekilde tamsayı alır.
    Hatalı girişlerde programın çökmesini engeller.
    """
    ok = False  # Döngüyü kontrol edecek bayrak (flag)
    
    while not ok:
        try:
            # Kullanıcıdan girdi al ve sayıya çevirmeyi dene
            value = int(input(prompt))
            ok = True  # Dönüşüm başarılıysa şimdilik döngüyü kırabiliriz
        except ValueError:
            # Eğer kullanıcı harf girdiyse program çökmez, buraya düşer
            print("Hata: Geçersiz bir sayı girdiniz.")
        
        if ok:
            # Sayı doğru aralıkta mı diye kontrol et
            ok = value >= min and value <= max
        
        if not ok:
            # Sayı aralık dışındaysa veya dönüşüm başarısızsa başa dön
            print(f"Hata: Girdiğiniz değer izin verilen aralıkta değil ({min}..{max})")
    
    return value

# Kullanım Örneği
v = read_int("Lütfen -10 ile 10 arasında bir sayı girin: ", -10, 10)
print("Seçtiğiniz güvenli sayı:", v)
```

---

## 4. İki Boyutlu Listeler (Matrisler) ve Küme (Set) Teorisi — Sudoku Doğrulayıcı

Matrisler, listelerin içinde listeler barındıran iki boyutlu veri yapılarıdır. Bir Sudoku oyununun kuralları matris işlemlerini anlamak için biçilmiş kaftandır.

Bir Sudoku tablosunun geçerli sayılması için:
1.  9 satırın her birinde 1-9 arası rakamlar benzersiz olmalı.
2.  9 sütunun her birinde 1-9 arası rakamlar benzersiz olmalı.
3.  Dokuz adet 3x3'lük alt karelerin her birinde 1-9 arası rakamlar benzersiz olmalı.

Burada Python'daki `set` (küme) yapısından faydalanacağız. Kümeler içindeki elemanları benzersiz tuttuğu için, elimizdeki sayıları `set("123456789")` ile kıyaslamak işimizi çok kolaylaştıracak.

```python
def sudoku_validator():
    """Kullanıcıdan alınan 9x9'luk Sudoku tablosunu doğrular."""
    
    def checkset(digs):
        """Verilen serinin 1-9 arası tüm rakamları eksiksiz içerdiğine bakar."""
        return set(digs) == set("123456789")

    rows = []

    # 1. Aşama: Kullanıcıdan 9 satırı da güvenli bir şekilde alalım
    for i in range(9):
        rowi = input(f"{i+1}. Satır için 9 basamaklı bir sayı girin: ")
        # Girdi 9 basamaklı ve tamamen sayılardan oluşana kadar zorla
        while len(rowi) != 9 or not rowi.isdigit():
            print("Geçersiz girdi! Lütfen tam olarak 9 rakam yazın.")
            rowi = input(f"{i+1}. Satır için TEKRAR girin: ")
        rows.append(rowi)

    # 2. Aşama: Satırları Kontrol Et
    for row in rows:
        if not checkset(row):
            print("Sonuç: GEÇERSİZ (Satır hatası)")
            return

    # 3. Aşama: Sütunları Kontrol Et (List Comprehension kullanarak sütunları çekiyoruz)
    for i in range(9):
        col = [row[i] for row in rows]
        if not checkset(col):
            print("Sonuç: GEÇERSİZ (Sütun hatası)")
            return

    # 4. Aşama: 3x3'lük Alt Kareleri Kontrol Et
    for r in range(0, 9, 3):
        for c in range(0, 9, 3):
            sqr = []
            for i in range(3):
                # Matris dilimleme (slicing) ile 3x3'lük parçaları topluyoruz
                sqr.extend(rows[r+i][c:c+3])
            if not checkset(sqr):
                print("Sonuç: GEÇERSİZ (3x3 Kare hatası)")
                return

    # Tüm testlerden geçtiyse
    print("Sonuç: TEBRİKLER! Sudoku tamamen geçerli.")
```

---

## 5. Tüm Programları Çalıştıran Ana Menü

Yukarıda yazdığımız tüm bu algoritmaları tek bir ana dosya üzerinden test etmek için projemizin en altına şu kontrol mekanizmasını ve ana menüyü ekliyoruz:

```python
def main():
    """Kullanıcının istediği programı seçmesini sağlayan ana menü."""
    print("\n--- ÇALIŞTIRMAK İSTEDİĞİNİZ PROGRAMI SEÇİN ---")
    print("1. Palindrome Checker (Tersten Okuma)")
    print("2. Anagram Checker (Harf Eşleştirme)")
    print("3. Digit of Life Calculator (Yaşam Sayısı)")
    print("4. Find a Word Game (Kelime Bulmaca)")
    print("5. Sudoku Validator (Sudoku Doğrulayıcı)")

    choice = input("Seçiminiz (1-5): ").strip()

    if choice == "1":
        palindrome()
    elif choice == "2":
        anagram()
    elif choice == "3":
        digit_of_life()
    elif choice == "4":
        find_word()
    elif choice == "5":
        sudoku_validator()
    else:
        print("Geçersiz bir seçim yaptınız.")

if __name__ == "__main__":
    main()
