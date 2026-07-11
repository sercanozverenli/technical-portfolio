# Diziler ve Veri İşleme

Seviye: Başlangıç  
Amaç: Tek boyutlu dizilerle temel veri işlemleri: eleman okuma/yazma, toplam/ortalama, min/max, arama, ters çevirme ve döndürme (rotate).

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Kodları kendi ortamınızda kullanmak isterseniz:
- Windows'ta bazı derleyiciler `conio.h` ve `getch()` sağlar; bu durumda ilgili satırları ekleyebilirsiniz.
- GCC (Linux/macOS) ve çoğu modern derleyicide `conio.h` yoktur; bekleme için `getchar()` kullanabilir veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- Tek boyutlu dizi tanımlama ve giriş/çıkış  
- Dizi toplamı ve ortalaması  
- En küçük / en büyük elemanı bulma  
- Lineer arama (linear search)  
- Diziyi ters çevirme (reverse) ve sağa/sola döndürme (rotate)

---

## A — Temel kavramlar

- Dizi bildirimi: `int a[10];` şeklinde sabit boyutlu dizi tanımlanır.  
- Eleman erişimi: `a[i]` ile i. eleman okunur/yazılır (0 tabanlı indeksleme).  
- Dizi uzunluğu: Sabit boyutlarda bir sabit veya değişken kullanın; fonksiyonlara boyutu ayrıca iletin.

---

## B — Örnek uygulamalar ve açıklamalar

### 1) Dizi toplamı ve karelerin toplamı / ortalama
Amaç: Dizi elemanlarının toplamını, kareler toplamını ve aritmetik ortalamasını hesaplama.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    printf("Dizi boyutu n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) {
        printf("%d. elemani girin: ", i + 1);
        if (scanf("%d", &a[i]) != 1) return 1;
    }

    long sum = 0;
    long sum_squares = 0;
    for (i = 0; i < n; ++i) {
        sum += a[i];
        sum_squares += (long)a[i] * a[i];
    }

    printf("Toplam = %ld\n", sum);
    printf("Kareler toplami = %ld\n", sum_squares);
    printf("Ortalama = %.2f\n", (double)sum / n);
    return 0;
}
```

Önemli not:
- Toplam hesaplarında taşma riskine karşı `long` kullanıldı.

---

### 2) Min / Max bulma
Amaç: Dizideki en küçük ve en büyük elemanı tespit etme.

Kod örneği:
```c
#include <stdio.h>
#include <limits.h>

int main(void) {
    int n, i;
    printf("Dizi boyutu n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) {
        if (scanf("%d", &a[i]) != 1) return 1;
    }

    int min = INT_MAX;
    int max = INT_MIN;
    for (i = 0; i < n; ++i) {
        if (a[i] < min) min = a[i];
        if (a[i] > max) max = a[i];
    }

    printf("Min = %d, Max = %d\n", min, max);
    return 0;
}
```

Önemli not:
- INT_MAX/INT_MIN sınırları taşma kontrolü için yardımcıdır.

---

### 3) Lineer arama (linear search)
Amaç: Bir değerin dizide olup olmadığını kontrol etme ve ilk konumunu döndürme.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i, target;
    printf("Dizi boyutu n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) {
        if (scanf("%d", &a[i]) != 1) return 1;
    }

    printf("Aranacak degeri girin: ");
    if (scanf("%d", &target) != 1) return 1;

    int pos = -1;
    for (i = 0; i < n; ++i) {
        if (a[i] == target) { pos = i; break; }
    }

    if (pos >= 0) printf("Deger bulundu, indeks = %d\n", pos);
    else printf("Deger dizide yok.\n");
    return 0;
}
```

Önemli not:
- Basit aramalar için lineer arama yeterlidir; sıralı dizilerde binary search daha hızlıdır.

---

### 4) Diziyi ters çevirme (reverse)
Amaç: Dizi elemanlarını yerinde tersine çevirme.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    printf("Dizi boyutu n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) if (scanf("%d", &a[i]) != 1) return 1;

    for (i = 0; i < n/2; ++i) {
        int tmp = a[i];
        a[i] = a[n - 1 - i];
        a[n - 1 - i] = tmp;
    }

    printf("Ters cevrilmis dizi: ");
    for (i = 0; i < n; ++i) printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```

Önemli not:
- Yerinde (in-place) algoritma ek hafıza gerektirir.

---

### 5) Diziyi sağa döndürme (rotate right by k)
Amaç: Diziyi sağa k pozisyon döndürme (rotate), basit O(n*k) yöntem ve notlar.

Kod örneği:
```c
#include <stdio.h>

void rotate_right(int a[], int n, int k) {
    k %= n;
    for (int r = 0; r < k; ++r) {
        int last = a[n-1];
        for (int i = n-1; i > 0; --i) a[i] = a[i-1];
        a[0] = last;
    }
}

int main(void) {
    int n, k;
    printf("Dizi boyutu n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (int i = 0; i < n; ++i) if (scanf("%d", &a[i]) != 1) return 1;

    printf("K degerini girin (saga kaydirma): ");
    if (scanf("%d", &k) != 1) return 1;

    rotate_right(a, n, k);

    printf("Sonuc: ");
    for (int i = 0; i < n; ++i) printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```

Önemli not:
- Bu basit yöntem O(n*k) zaman alır; büyük k için O(n) algoritmalar (reverse-trick) tercih edilir.

---

## C — Küçük ipuçları

1) Dizi boyutunu fonksiyonlara parametre olarak gönderin; sabit varsayımlara güvenmeyin.  
2) Bellek sınırlarına dikkat edin; büyük dizilerde dinamik bellek (malloc) kullanımı gerekebilir.  
3) İndekslerin 0 tabanlı olduğunu unutmayın; off-by-one hatalar sık görülür.  
4) Sıralı arama yerine arama sık yapılacaksa diziyi sıralayıp binary search kullanmayı düşünün.
