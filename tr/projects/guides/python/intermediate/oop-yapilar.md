# Python'da Nesne Yönelimli Programlama (OOP) ve Temel Veri Yapıları

Bu bölümde yazılım dünyasında soyut kavramları somutlaştırmak için kullandığımız Nesne Yönelimli Programlama (OOP) ilkelerini ve bilgisayar bilimlerinin temel taşları olan veri yapılarını inceleyeceğiz. 

Ele alacağımız konular:
- Kapsülleme (Encapsulation) ve gizli (private) değişkenler.
- Kalıtım (Inheritance) ve mevcut metotları ezerek genişletme (Method Overriding).
- LIFO (Yığın) ve FIFO (Kuyruk) veri yapılarının Python listeleri ile tasarlanması.
- Sihirli Metotlar (Magic Methods) ve nesne durum yönetimi.

---

## 1. Temel OOP, Kapsülleme ve Yığın (Stack) Yapısı

Bilgisayar bilimlerinde Yığın (Stack), verilerin üst üste elbiseler gibi dizildiği bir yapıdır. Bu yapı **LIFO (Last In, First Out - Son Giren İlk Çıkar)** prensibiyle çalışır. Yani yığına en son eklediğiniz eleman, yığından alacağınız ilk elemandır. Bunu bir kutu içindeki kitaplar gibi düşünebilirsiniz; en üstteki kitabı almadan alttakilere ulaşamazsınız.

Python'da bir sınıf oluştururken verilerimizi dış dünyadan gizlemek ve doğrudan değiştirilmesini engellemek için çift alt çizgi (`__`) kullanırız. Buna **Kapsülleme (Encapsulation)** denir. Aşağıdaki örnekte `__stk` listesi gizli tutularak dışarıdan rastgele müdahalelere kapatılmıştır.

```python
class Stack:
    """
    Basit bir yığın (LIFO: Son Giren İlk Çıkar) uygulaması.
    Bu sınıf temel ekleme (push) ve çıkarma (pop) işlemlerini sağlar.
    """

    def __init__(self):
        self.__stk = []  # Elemanları tutacak gizli liste

    def push(self, val):
        """
        Yığının en üstüne yeni bir eleman ekler.
        """
        self.__stk.append(val)  # Elemanı listenin sonuna ekliyoruz

    def pop(self):
        """
        Yığının en üstündeki elemanı çıkarır ve geri döndürür.
        """
        val = self.__stk[-1]    # Son elemanı al
        del self.__stk[-1]      # Son elemanı listeden sil
        return val
```

---

## 2. Sınıf Kalıtımı (Inheritance) ve Metot Ezme (Overriding)

Bazen yazdığımız temel bir sınıfın özelliklerini koruyarak ona yeni yetenekler kazandırmak isteriz. Sıfırdan kod yazmak yerine, mevcut sınıftan miras alarak ilerleriz. Buna **Kalıtım (Inheritance)** denir.

Eğer miras aldığımız sınıftaki bir fonksiyonun davranışını değiştirmek veya genişletmek istersek, o fonksiyonu yeni sınıfta aynı isimle tekrar tanımlarız. Buna da **Metot Ezme (Method Overriding)** denir.

Aşağıdaki `CountingStack` sınıfı, yukarıdaki temel `Stack` sınıfından miras alır. Amacı, yığın işlemlerini aynen yaparken arka planda kaç defa ekleme ve çıkarma yapıldığını saymaktır.

```python
class CountingStack(Stack):
    """
    Ekleme ve çıkarma işlemlerini sayan gelişmiş yığın sınıfı.
    Yığın kullanımını izlemek için uygundur.
    """
    def __init__(self):
        Stack.__init__(self)     # Ana sınıfın başlatıcısını çağırıyoruz
        self.__push_counter = 0  # Ekleme işlemi için sayaç
        self.__pop_counter = 0   # Çıkarma işlemi için sayaç

    def get_push_counter(self):
        """
        Yapılan toplam ekleme işleminin sayısını döndürür.
        """
        return self.__push_counter

    def get_pop_counter(self):
        """
        Yapılan toplam çıkarma işleminin sayısını döndürür.
        """
        return self.__pop_counter

    def total_operations(self):
        """
        Yapılan toplam işlem sayısını (ekleme + çıkarma) döndürür.
        """
        return self.__push_counter + self.__pop_counter

    def push(self, val):
        """
        Yığına eleman ekler ve ekleme sayacını bir artırır.
        """
        self.__push_counter += 1
        return Stack.push(self, val)  # Ana sınıftaki push metodunu çağırıyoruz

    def pop(self):
        """
        Yığından eleman çıkarır ve çıkarma sayacını bir artırır.
        """
        self.__pop_counter += 1
        return Stack.pop(self)  # Ana sınıftaki pop metodunu çağırıyoruz


# Kullanım Örneği
if __name__ == "__main__":
    yigin = CountingStack()
    for i in range(100):
        yigin.push(i)
        yigin.pop()

    print("Ekleme sayısı:", yigin.get_push_counter())       # Çıktı: 100
    print("Çıkarma sayısı:", yigin.get_pop_counter())       # Çıktı: 100
    print("Toplam işlem sayısı:", yigin.total_operations())  # Çıktı: 200
```

---

## 3. Kuyruk (Queue) Yapısı ve FIFO Prensibi

Kuyruk (Queue), yığının tam tersi bir mantıkla çalışır. Gerçek hayattaki bir fırın veya banka kuyruğu gibi, **FIFO (First In, First Out - İlk Giren İlk Çıkar)** prensibine dayanır. Kuyruğa ilk giren kişi işlemini ilk tamamlar ve ayrılır.

Python listelerinde bunu simüle etmek için yeni gelen elemanı `insert(0, eleman)` ile listenin en başına (0. indekse) ekleriz. Eleman çıkarırken ise listenin sonundaki elemanı `pop()` ile alırız. Böylece ilk eklenen eleman en sağda kalacağı için ilk o çıkmış olur.

```python
class Queue:
    """
    Basit bir kuyruk uygulaması (FIFO - İlk Giren İlk Çıkar).
    """
    def __init__(self):
        self._queue = []  # Elemanları saklayacağımız liste
    
    def put(self, elem):
        """Kuyruğa yeni eleman ekler."""
        self._queue.insert(0, elem)  # Yeni elemanı listenin en başına yerleştir
    
    def get(self):
        """Kuyruktaki sıradaki elemanı çıkarır ve döndürür."""
        return self._queue.pop()     # Listenin sonundaki (en eski) elemanı çıkar
    
    def isempty(self):
        """Kuyruk boşsa True, doluysa False döndürür."""
        return not self._queue       # Liste boşsa True döner
        

# Kullanım Örneği
kuyruk = Queue()
kuyruk.put(1)        # Sayı ekle
kuyruk.put("köpek")  # Metin ekle
kuyruk.put(False)    # Mantıksal değer ekle

for i in range(4):   # 4 defa eleman çekmeyi deneyelim
    if not kuyruk.isempty():
        print(kuyruk.get())  # Kuyruktaki elemanı yazdır
    else:
        print("Kuyruk boş")  # Kuyrukta eleman kalmadıysa uyar
```

*Not: Büyük verilerle çalışırken standart Python listelerinde `insert(0)` işlemi tüm elemanları sağa kaydırdığı için yavaştır. Performans kritik durumlarda bunun yerine `collections.deque` yapısı tercih edilir.*

---

## 4. Özel Metotlar (Magic Methods) ve Durum Yönetimi

Python'da sınıfların içinde çift alt çizgi ile başlayan ve biten `__str__`, `__init__` gibi metotlara **Sihirli Metotlar (Magic Methods)** denir. Bu metotlar, nesnelerin dilin kendi yerleşik fonksiyonlarına nasıl yanıt vereceğini belirler.

Örneğin, oluşturduğumuz bir nesneyi direkt `print(nesne)` şeklinde ekrana yazdırmak istediğimizde Python arka planda `__str__` metodunu arar. Eğer bu metodu sınıf içinde tanımlarsak, nesnenin ekranda nasıl bir formatta görüneceğini kendimiz belirleyebiliriz.

Aşağıdaki `Timer` sınıfı, saat, dakika ve saniye değerlerini tutarak bir zamanlayıcıyı modeller. Zamanın ileri veya geri akması durumunda oluşabilecek sınır aşımı (23:59:59'dan sonra 00:00:00 olması gibi) durumlarını yönetir.

```python
def two_digits(val):
    """
    Bir sayıyı iki basamaklı metne dönüştürür.
    Sayı tek basamaklıysa başına sıfır ekler.
    Örnek: 5 -> "05", 12 -> "12"
    """
    s = str(val)
    if len(s) == 1:
        s = '0' + s
    return s


class Timer:
    """
    Saat, dakika ve saniye cinsinden zamanı tutan zamanlayıcı sınıfı.
    """

    def __init__(self, hours=0, minutes=0, seconds=0):
        """
        Yeni bir Zamanlayıcı nesnesi oluşturur.
        Saat   : 0–23
        Dakika : 0–59
        Saniye : 0–59
        """
        self.__hours = hours       # Gizli saat değeri
        self.__minutes = minutes   # Gizli dakika değeri
        self.__seconds = seconds   # Gizli saniye değeri

    def __str__(self):
        """
        Zamanı ss:dk:sn formatında bir metin olarak döndürür.
        print() fonksiyonu çağrıldığında bu çıktı tetiklenir.
        """
        return (
            two_digits(self.__hours) + ":" +
            two_digits(self.__minutes) + ":" +
            two_digits(self.__seconds)
        )

    def next_second(self):
        """
        Zamanlayıcıyı bir saniye ileri taşır.
        """
        self.__seconds += 1                    # Saniyeyi artır

        if self.__seconds > 59:                # Saniye sınırı aşıldıysa
            self.__seconds = 0
            self.__minutes += 1                # Dakikayı artır

            if self.__minutes > 59:            # Dakika sınırı aşıldıysa
                self.__minutes = 0
                self.__hours += 1              # Saati artır

                if self.__hours > 23:          # Saat sınırı aşıldıysa (Gün bittiyse)
                    self.__hours = 0

    def prev_second(self):
        """
        Zamanlayıcıyı bir saniye geri taşır.
        """
        self.__seconds -= 1                    # Saniyeyi azalt

        if self.__seconds < 0:                 # Saniye sıfırın altına düştüyse
            self.__seconds = 59
            self.__minutes -= 1                # Dakikayı azalt

            if self.__minutes < 0:             # Dakika sıfırın altına düştüyse
                self.__minutes = 59
                self.__hours -= 1              # Saati azalt

                if self.__hours < 0:           # Saat sıfırın altına düştüyse
                    self.__hours = 23


# Test Kodları
zamanlayici = Timer(23, 59, 59)
print(zamanlayici)          # Çıktı: 23:59:59

zamanlayici.next_second()
print(zamanlayici)          # Çıktı: 00:00:00 (Yeni güne geçiş kontrolü başarılı)

zamanlayici.prev_second()
print(zamanlayici)          # Çıktı: 23:59:59 (Geriye doğru sınır kontrolü başarılı)
```
