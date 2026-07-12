# Basit Algoritmalar ve Karmaşıklık

Bu sayfada temel algoritmaların mantığını, küçük örnek uygulamalarını ve algoritmaların zaman/mekân karmaşıklığı hakkında temel bilgileri öğreneceksiniz. Anlatım konu odaklıdır: önce kavramı açıklıyor, sonra uygulama örneği ve pratik not veriyorum.

Bu sayfada öğrenecekleriniz
- Algoritma nedir ve neden önemlidir
- Zaman karmaşıklığı (Big O) hakkında temel anlayış
- Sıralama: bubble sort örneği ve optimizasyonu
- Arama: doğrusal (linear) arama ve ikili (binary) arama farkı
- Küçük pratik öneriler ve ne zaman hangi algoritmayı seçmeli

---

## Algoritma nedir ve neden önemlidir

Algoritma, belirli bir problemi çözmek için izlenen adımlar dizisidir. Aynı problemi farklı algoritmalarla çözebilirsiniz; bu algoritmaların verimliliği (hız, bellek kullanımı) farklı olabilir. Gerçek dünyada özellikle büyük veri ile çalışırken iyi bir algoritma seçmek performans açısından kritiktir.

---

## Zaman karmaşıklığı (Big O) — temel fikir

Zaman karmaşıklığı, bir algoritmanın giriş boyutu (genellikle `n`) arttıkça çalışma süresinin nasıl büyüdüğünü ifade eder. Bazı yaygın sınıflar:
- O(1): sabit zaman — giriş büyüklüğünden bağımsız.
- O(log n): logaritmik — ör. binary search.
- O(n): doğrusal — ör. tek bir geçişle arama.
- O(n log n): iyi sıralama algoritmaları (örn. mergesort, quicksort ortalama).
- O(n^2): kare zaman — ör. basit sıralama yöntemleri (bubble sort, selection sort).

Pratik not: küçük `n` için O(n^2) yöntemler kabul edilebilir; ancak `n` büyüdükçe O(n^2) hızla maliyetli hale gelir.

---

## Sıralama: Bubble Sort (balon sıralama)

Bubble sort, en basit sıralama algoritmalarından biridir. Mantık:
- Dizideki bitişik eleman çiftlerini dolaş, eğer yanlış sıradaysa takas et.
- Her geçişte en büyük (veya en küçük) eleman dizinin sonuna "balon" gibi çıkar.
- Bu işlemi dizi tamamen sıralanana kadar tekrarla.

Avantaj: Anlaşılması kolay, küçük veri için kullanılabilir.
Dezavantaj: O(n^2) zaman karmaşıklığı — büyük veri için yavaş.

Basit implementasyon
```python
# Bubble sort: küçükten büyüğe sıralama

def bubble_sort(a):
    n = len(a)
    # dış döngü: her geçişte en az bir eleman yerine oturur
    for i in range(n):
        # iç döngü: henüz sağlamlaşmamış kısımda dolaş
        for j in range(0, n - i - 1):
            if a[j] > a[j + 1]:
                # yer değiştir
                a[j], a[j + 1] = a[j + 1], a[j]

# kullanım
arr = [5, 2, 9, 1, 5]
bubble_sort(arr)
print(arr)  # [1, 2, 5, 5, 9]
```

Basitleştirilmiş optimizasyon: eğer bir geçişte hiç takas olmadıysa dizi zaten sıralıdır; döngüyü erken sonlandırabilirsiniz.

```python
def bubble_sort_optimized(a):
    n = len(a)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if a[j] > a[j + 1]:
                a[j], a[j + 1] = a[j + 1], a[j]
                swapped = True
        if not swapped:
            break
```

---

## Arama: Linear (doğrusal) arama vs Binary (ikili) arama

Linear search (doğrusal arama)
- Bir dizide aranan öğeyi baştan sona kontrol eder.
- Zaman karmaşıklığı O(n).
- Sıralı olmayan veri üzerinde doğrudan çalışır.

```python
# doğrusal arama

def linear_search(a, target):
    for i, val in enumerate(a):
        if val == target:
            return i
    return -1
```

Binary search (ikili arama)
- Sadece sıralı (sorted) dizilerde çalışır.
- Diziyi ortadan bölerek aramayı daraltır; zaman karmaşıklığı O(log n).
- Dizinin ortanca elemanına bak, eğer aradığın daha küçükse sol tarafta devam et, değilse sağ tarafta.

```python
# ikili arama (döngüsel versiyon)

def binary_search(a, target):
    left, right = 0, len(a) - 1
    while left <= right:
        mid = (left + right) // 2
        if a[mid] == target:
            return mid
        elif a[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

# kullanım: a sıralı olmalı
arr = [1, 2, 5, 7, 9]
print(binary_search(arr, 7))  # 3
```

Ne zaman hangi aramayı seçmeli?
- Veri sıralıysa ve çok büyükse binary search çok daha verimlidir.
- Eğer veri sıralı değilse ve tek seferlik arama yapacaksanız linear search daha pratiktir; çok sayıda arama yapacaksanız veriyi bir kez sıralayıp(binary searchable) binary search kullanmak mantıklı olabilir.

---

## Küçük pratik öneriler

- Küçük listeler için basit algoritmalar (`bubble_sort`) kabul edilebilir; gerçek uygulamalarda Python'ın gömülü `sorted()` işlevini kullanın — tipik olarak O(n log n) karmaşıklığa sahip iyi optimize edilmiş algoritmalar kullanır.
- Karmaşıklık analizini yaparken hem zaman hem de bellek (space complexity) göz önünde bulundurun.
- Bir problemin gereksinimine göre algoritma seçin: eğer sıralama gerekmiyorsa arama için uygun veri yapısını (set, dict) kullanmak çok daha hızlı olabilir (örneğin membership test için `in` set yapısı O(1) ortalama zaman sağlar).
