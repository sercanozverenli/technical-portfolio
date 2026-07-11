# Döngüler ve Yineleme

Seviye: Başlangıç  
Amaç: C'de döngü yapıları (for / while), iç içe döngüler, döngü ile örüntü üretme ve temel algoritmik düşünceyi öğrenmek.

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Kodları kendi ortamınızda kullanmak isterseniz:
- Windows'ta bazı derleyiciler `conio.h` ve `getch()` sağlar; bu durumda ilgili satırları ekleyebilirsiniz.
- GCC (Linux/macOS) ve çoğu modern derleyicide `conio.h` yoktur; bekleme için `getchar()` kullanabilir veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- `for` ve `while` döngüleri  
- İç içe (nested) döngüler  
- Döngü sayacı ve koşul kontrolü  
- Döngülerle desen (pattern) oluşturma  
- Döngülerde common pitfalls (sonsuz döngü, tip kullanımı)

---

## A — Temel kavramlar

### 1) for döngüsü
- Yapısı: `for (başlangıç; koşul; artış) { /* ... */ }`  
- Tipik kullanım: belirli sayıda yineleme.

### 2) while döngüsü
- Yapısı: `while (koşul) { /* ... */ }`  
- Tipik kullanım: koşul sağlandığı sürece çalıştır; yineleme sayısı önceden bilinmiyorsa uygundur.

### 3) İç içe döngüler
- İç döngü her dış döngü adımı için baştan çalışır; matris ve desen üretimleri için kullanılır.  
- Karmaşıklık: iki iç içe döngü O(n*m) zaman maliyetine sahiptir.

### 4) Döngü hataları
- Sonsuz döngü: koşulun hiçbir zaman false olmaması.  
- Sayaç tipi: sayaç için uygun tip seçilmezse taşma veya yanlış sonuç olabilir (ör. sayacın int olması gereken yerde float kullanılması).

---

## B — Örnek uygulamalar ve açıklamalar

Her örnekte: Amaç → Açıklama → Kod örneği → Önemli notlar

### Örnek 1 — 1..n sayılarının toplamı ve kareler toplamı
Amaç: Basit `for` döngüsüyle toplama; kare alma işlemi ile aritmetik uygulama.

Açıklama: Kullanıcıdan n alınır; 1'den n'e kadar toplam ve karelerin toplamı hesaplanır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    long sum = 0;
    long sum_squares = 0;

    printf("Bir pozitif tamsayi (n) girin: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 1; i <= n; ++i) {
        sum += i;
        sum_squares += i * i;
    }

    printf("1..%d arasi sayilarin toplami = %ld\n", n, sum);
    printf("1..%d arasi sayilarin kareleri toplami = %ld\n", n, sum_squares);

    return 0;
}
```

Önemli not:
- Toplamlar büyük olabilir; `int` yerine `long` kullanmak taşmayı azaltır.

---

### Örnek 2 — Ortalama hesaplama
Amaç: Döngü ile n adet sayıyı okumak ve ortalamasını almak.

Açıklama: Kullanıcı kaç sayı gireceğini belirtir; döngü içinde her sayıyı toplayıp sonunda ortalama hesaplanır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int i, n;
    double total = 0.0, number;

    printf("Kac sayi gireceksiniz? ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 0; i < n; ++i) {
        printf("%d. sayiyi girin: ", i + 1);
        if (scanf("%lf", &number) != 1) return 1;
        total += number;
    }

    printf("Ortalama = %.2f\n", total / n);
    return 0;
}
```

Önemli not:
- `total` ve `number` için `double` kullanmak ondalıklı girişleri destekler.

---

### Örnek 3 — Toplama ile çarpma (çarpmayı toplama ile yapma)
Amaç: Döngü kavramını pekiştirmek; bir sayıyı diğerine tekrar ekleyerek çarpma gerçekleştirme.

Açıklama: `a * b` işlemini doğrudan kullanmak yerine `b` değerini `a` kere toplayarak sonuç elde edilir. Negatif ve ondalık durumlar için ek kontroller gerekir; burada pozitif tam sayılar için basit örnek verilmiştir.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int a, b, i;
    long result = 0;

    printf("Carpan (a) girin (pozitif tamsayi): ");
    if (scanf("%d", &a) != 1 || a < 0) return 1;

    printf("Carpilan (b) girin (tamsayi): ");
    if (scanf("%d", &b) != 1) return 1;

    for (i = 0; i < a; ++i) {
        result += b;
    }

    printf("Result = %ld\n", result);
    return 0;
}
```

Önemli not:
- Negatif sayılar veya ondalık sayılar için algoritma genişletilmeli; örneğin işaret ayrıştırması yapılmalı.

---

### Örnek 4 — 5x5 çarpım tablosu (her hücrede i*j + 1)
Amaç: İç içe `for` kullanımı ile matris benzeri çıktı oluşturma.

Açıklama: İki döngü (satır ve sütun) kullanılarak tablo üretilir; her değer `(i * j) + 1` şeklinde hesaplanır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int i, j;

    for (i = 1; i <= 5; ++i) {
        for (j = 1; j <= 5; ++j) {
            printf("%5d", i * j + 1);
        }
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- İç içe döngülerde formatlama ile hizalama sağlamak çıktıyı okunur kılar.

---

### Örnek 5 — Ters sayı üçgeni (Reverse number triangle)
Amaç: Döngü ile desen oluşturma, iç içe döngü kullanımı.

Açıklama: Her satırda geriye doğru sayılar yazılır; dış döngü satır sayısını, iç döngü sayıları üretir.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i, j;

    printf("Satir sayisini girin: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 1; i <= n; ++i) {
        for (j = i; j >= 1; --j) {
            printf("%3d", j);
        }
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- İç döngü geriye doğru saydığı için `j` başlangıcı `i` olmalıdır.

---

### Örnek 6 — Piramit yıldız deseni (Pyramid star pattern)
Amaç: Boşluk ve yıldız yazımıyla merkezlenmiş piramit oluşturma.

Açıklama: Her satırda önce boşluklar yazılır, sonra yıldız karakterleri `2*i - 1` formülüne göre yazılır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i, j;

    printf("Satir sayisini girin: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 1; i <= n; ++i) {
        for (j = 1; j <= n - i; ++j) {
            printf(" ");
        }
        for (j = 1; j <= (2 * i - 1); ++j) {
            printf("*");
        }
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- Çıktı genişliği küçük terminal pencerelerinde bozulabilir; hizalamayı korumak için monospace fontlu ve yeterli genişlikte terminal kullanın.

---

### Örnek 7 — Basamak toplamı ve sayı ters çevirme (Digit sum and reverse)
Amaç: `while` döngüsü ile bir sayının basamaklarını işleme; mod ve bölme operatörleri kullanımı.

Açıklama: Sayının her basamağı `n % 10` ile elde edilir; `reverse = reverse * 10 + digit` ile ters sayı oluşturulur ve `sum += digit` ile basamak toplamı hesaplanır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, temp, digit;
    int sum = 0;
    int reverse = 0;

    printf("Bir tamsayi girin: ");
    if (scanf("%d", &n) != 1) return 1;

    temp = n;
    if (temp < 0) temp = -temp; /* negatif sayi icin isaret alma, gerekirse uyari ekleyin */

    while (temp > 0) {
        digit = temp % 10;
        sum += digit;
        reverse = reverse * 10 + digit;
        temp = temp / 10;
    }

    printf("Basamak toplami = %d\n", sum);
    printf("Ters sayi = %d\n", reverse);
    return 0;
}
```

Önemli not:
- Negatif sayılar için ek mantık eklenebilir (işaretin korunması gibi).

---

## C — Küçük ipuçları

1) Döngü sınırlarını dikkatle belirleyin; `<=` ve `<` farkı off-by-one hatalarına yol açar.  
2) İç içe döngülerde performansın N² olabileceğini unutmayın; büyük verilerde alternatif algoritma düşünün.  
3) Döngü içindeki ağır işlemleri mümkünse döngü dışına çıkarın (ör. sabit ifadeler).  
4) Sonsuz döngüye dikkat: `for (;;)` bilinçli kullanılmadıkça kaçının ve çıkış koşulu sağlayın.

