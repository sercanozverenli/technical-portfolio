# Fonksiyonlar ve Modülerlik

Seviye: Başlangıç  
Amaç: C'de fonksiyon tanımlama ve kullanma, parametre ve dönüş tipleri, kodu modüler hale getirme, sık kullanılan örnek fonksiyonlar (üs alma, faktöriyel, kombinasyon, seri hesapları).

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Kodları kendi ortamınızda kullanmak isterseniz:
- Windows'ta bazı derleyiciler `conio.h` ve `getch()` sağlar; bu durumda ilgili satırları ekleyebilirsiniz.
- GCC (Linux/macOS) ve çoğu modern derleyicide `conio.h` yoktur; bekleme için `getchar()` kullanabilir veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- Fonksiyon bildirimi (declaration) ve tanımı (definition)  
- Parametreler, dönüş tipleri ve yerel değişkenler  
- Modüler kod yazımı: tekrar eden kodu fonksiyona alma  
- Örnek fonksiyonlar: üs (power), faktöriyel, kombinasyon (nCr), e^x serisi, kosinüs serisi

---

## A — Temel kavramlar

### 1) Fonksiyon bildirimi ve tanımı
- Bildirim (prototip) fonksiyonun imzasını belirtir: `float power(int x, int n);`  
- Tanım fonksiyonun gövdesidir:
  ```c
  float power(int x, int n) {
      float result = 1.0f;
      for (int i = 0; i < n; ++i) result *= x;
      return result;
  }
  ```
- `main` içinden fonksiyon çağırılır: `s = power(2, 3);`

### 2) Parametreler ve dönüş tipi
- Parametreler değer olarak geçirilir (pass-by-value).  
- Dönen değer fonksiyonun sonuç tipidir; geri dönüş `return` ile yapılır.  
- Yerel değişkenler fonksiyon içinde yaşar; fonksiyon bitince yok olurlar.

### 3) Modülerlik
- Tekrar eden işlemleri fonksiyon haline getirerek kod tekrarı azaltılır ve okunurluk artar.  
- Fonksiyonlar küçük, tek sorumluluğa sahip olmalıdır (single responsibility).

---

## B — Örnek fonksiyonlar ve açıklamalar

### Örnek 1 — Üs alma fonksiyonu (power)
Amaç: Bir sayıyı üssünü almak için fonksiyon yazmak; döngü ve dönüş tipi kullanımı.

Açıklama: `power(x, n)` fonksiyonu `x`'i `n` kez çarparak döndürür.

Kod örneği:
```c
#include <stdio.h>

float power(int x, int n) {
    float result = 1.0f;
    for (int i = 0; i < n; ++i) {
        result *= x;
    }
    return result;
}

int main(void) {
    int x, n;
    printf("Taban (x) girin: ");
    if (scanf("%d", &x) != 1) return 1;
    printf("Us (n) girin: ");
    if (scanf("%d", &n) != 1) return 1;

    float s = power(x, n);
    printf("%d uzeri %d = %.2f\n", x, n, s);
    return 0;
}
```

Önemli not:
- Bu basit fonksiyon yalnızca pozitif tam üsler için uygundur; negatif üs veya ondalık taban gerekiyorsa genişletin.

---

### Örnek 2 — Faktöriyel ve kombinasyon (nCr)
Amaç: Faktöriyel fonksiyonu ile kombinasyon hesaplamak; fonksiyonların yeniden kullanılabilirliğini göstermek.

Açıklama: `factorial(k)` fonksiyonu `k!` hesaplar. Kombinasyon `C(n,r) = n! / (r! * (n-r)!)`.

Kod örneği:
```c
#include <stdio.h>

double factorial(int k) {
    double f = 1.0;
    for (int i = 2; i <= k; ++i) {
        f *= i;
    }
    return f;
}

int main(void) {
    int n, r;
    printf("n degerini girin: ");
    if (scanf("%d", &n) != 1) return 1;
    printf("r degerini girin: ");
    if (scanf("%d", &r) != 1) return 1;

    if (r < 0 || r > n) {
        printf("Gecersiz r degeri.\n");
        return 1;
    }

    double comb = factorial(n) / (factorial(r) * factorial(n - r));
    printf("Kombinasyon C(%d,%d) = %.0f\n", n, r, comb);
    return 0;
}
```

Önemli not:
- Büyük `n` değerlerinde faktöriyel hızla büyür; daha güvenli hesaplama için ardışık çarpımlar veya log tabanlı yöntemler tercih edilebilir.

---

### Örnek 3 — e^x hesaplama (seriler ile)
Amaç: Fonksiyonları kullanarak e^x serisini hesaplamak (Taylor serisi).

Açıklama: e^x ≈ 1 + x/1! + x^2/2! + ... + x^n/n!. `power` ve `factorial` fonksiyonlarını tekrar kullanabiliriz.

Kod örneği:
```c
#include <stdio.h>

double factorial(int k) {
    double f = 1.0;
    for (int i = 2; i <= k; ++i) f *= i;
    return f;
}

double power(int x, int n) {
    double result = 1.0;
    for (int i = 0; i < n; ++i) result *= x;
    return result;
}

int main(void) {
    int n, x;
    printf("Seri terim sayisi (n) girin: ");
    if (scanf("%d", &n) != 1) return 1;
    printf("x degerini girin: ");
    if (scanf("%d", &x) != 1) return 1;

    double e = 1.0;
    for (int i = 1; i <= n; ++i) {
        e += power(x, i) / factorial(i);
    }
    printf("e uzeri %d (yaklasik) = %f\n", x, e);
    return 0;
}
```

Önemli not:
- `power` burada `int` taban için yazıldı; daha genel kullanım için taban `double` yapılabilir.

---

### Örnek 4 — Kosinüs hesaplama (seri ile)
Amaç: Fonksiyonlar ve döngülerle kosinüs serisini hesaplamak.

Açıklama: cos(x) ≈ 1 - x^2/2! + x^4/4! - ... İşaret değişimini takip etmek için `sign` kullanılır.

Kod örneği:
```c
#include <stdio.h>

double power(double x, int n) {
    double result = 1.0;
    for (int i = 0; i < n; ++i) result *= x;
    return result;
}

double factorial(int n) {
    double f = 1.0;
    for (int i = 2; i <= n; ++i) f *= i;
    return f;
}

int main(void) {
    int n, x;
    printf("Seri terim sayisi (n) girin: ");
    if (scanf("%d", &n) != 1) return 1;
    printf("x degerini girin (radyan olarak dusunun): ");
    if (scanf("%d", &x) != 1) return 1;

    double cosine = 1.0;
    int sign = -1;
    for (int i = 2; i <= 2 * n; i += 2) {
        cosine += sign * power(x, i) / factorial(i);
        sign = -sign;
    }
    printf("Cosine(%d) (yaklasik) = %f\n", x, cosine);
    return 0;
}
```

Önemli not:
- Seri yaklaşımı için `x` genellikle radyan olmalıdır; ayrıca `power` ve `factorial` daha büyük `n` için taşma/performans sorunları yaşayabilir.

---

## C — Küçük ipuçları

1) Fonksiyonları küçük, tek iş yapan parçalara ayırın; bu test ve yeniden kullanım kolaylığı sağlar.  
2) Fonksiyon prototiplerini dosya başına koymak (header) ve tanımları ayrı tutmak büyük projelerde iyi pratiktir.  
3) Değer geçişinin kopyalama olduğunu unutmayın; büyük veri yapıları için işaretçi (pointer) kullanmak performans açısından önemlidir.  
4) Rekürsiyon (özellikle faktöriyel) kullanırken yığın (stack) sınırlarını göz önünde bulundurun; iteratif çözümler genellikle daha güvenlidir.
