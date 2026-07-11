# Değişkenler ve Temel Giriş/Çıkış

Seviye: Başlangıç  
Amaç: C'de değişkenleri tanımak, kullanıcıdan veri okumayı (scanf), ekrana yazdırmayı (printf), temel veri tiplerini ve basit aritmetiği öğrenmek.

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Kodları kendi ortamınızda kullanmak isterseniz:
- Windows'ta bazı derleyiciler `conio.h` ve `getch()` sağlar; bu durumda ilgili satırları ekleyebilirsiniz.
- GCC (Linux/macOS) ve çoğu modern derleyicide `conio.h` yoktur; bekleme için `getchar()` kullanabilir veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- Temel veri tipleri: int, float, double, char  
- printf ve scanf kullanımı, format belirteçleri  
- Tip dönüşümleri (casting) ve tam sayı bölmesi vs. ondalık bölme  
- Sabitler ve makrolar (`#define`)  
- Örnek uygulamalar: üçgen alanı, dikdörtgen çevresi, dairenin çevresi ve alanı

---

## A — Temel kavramlar

### 1) Veri tipleri
- `int`: Tam sayılar (ör. 1, -5, 100)  
- `float` / `double`: Ondalıklı sayılar (ör. 3.14). `double` daha yüksek doğruluk sağlar.  
- `char`: Tek karakter (ör. 'a')  
- Örnek bildirim: `int n; float x; char c;`

### 2) printf ve scanf
- `printf` ile ekrana yazdırılır; format belirteçleri: `%d` (int), `%f` (float), `%lf` (double), `%c` (char).  
- `scanf` ile kullanıcıdan okunur; dönüş değeri kontrol edilmelidir:
  - `if (scanf("%d", &n) != 1) return 1;`

### 3) Tip dönüşümleri (casting)
- `int / int` işlemi tam sayı bölmesi yapar; ondalıklı sonuç isteniyorsa en az bir operand `float`/`double` olmalı veya cast kullanılmalıdır:
  - Örnek: `area = (float)(base * height) / 2.0f;`

### 4) Sabitler ve makrolar
- `#define PI 3.14f` ile sabit tanımlanır. Daha hassas hesaplar için daha fazla basamak veya `double` tercih edin.  
- Alternatif: `const double pi = 3.141592653589793;`

---

## B — Örnek uygulamalar ve açıklamalar

Her örnekte: Amaç → Açıklama → Kod örneği → Önemli notlar

### Örnek 1 — Üçgenin alanı
Amaç: Değişken tanımlama, kullanıcı girişi, casting ile doğru bölme.

Açıklama: Üçgen alanı = (taban * yükseklik) / 2. `base` ve `height` tam sayıysa ondalık sonuç almak için cast gerekir.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int base, height;
    float area;

    printf("Taban uzunluğunu girin: ");
    if (scanf("%d", &base) != 1) return 1;

    printf("Yüksekliği girin: ");
    if (scanf("%d", &height) != 1) return 1;

    area = (float)(base * height) / 2.0f;
    printf("Alan: %.2f\n", area);

    return 0;
}
```

Önemli not:
- Cast `(float)` yapılmazsa tam sayı bölmesi nedeniyle beklenmeyen sonuç elde edilebilir.

### Örnek 2 — Dikdörtgen çevresi
Amaç: `float` kullanımı ve temel aritmetik.

Açıklama: Çevre = 2 * (side1 + side2) veya `2*side1 + 2*side2`.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    float side1, side2, perimeter;

    printf("Birinci kenarı girin: ");
    if (scanf("%f", &side1) != 1) return 1;

    printf("İkinci kenarı girin: ");
    if (scanf("%f", &side2) != 1) return 1;

    perimeter = 2.0f * side1 + 2.0f * side2;
    printf("Çevre: %.2f\n", perimeter);

    return 0;
}
```

Önemli not:
- `float` kullanımı ondalıklı kenar uzunluklarını destekler; hassasiyet için `double` tercih edilebilir.

### Örnek 3 — Dairenin çevresi ve alanı
Amaç: Makro ile sabit kullanım, birden çok değerin hesaplanması.

Açıklama: Çevre = 2 * PI * r, Alan = PI * r * r.

Kod örneği:
```c
#include <stdio.h>
#define PI 3.14f

int main(void) {
    float radius, circumference, area;

    printf("Yarıçapı girin: ");
    if (scanf("%f", &radius) != 1) return 1;

    circumference = 2.0f * PI * radius;
    area = PI * radius * radius;

    printf("Çevre: %.2f | Alan: %.2f\n", circumference, area);

    return 0;
}
```

Önemli not:
- Daha hassas sonuçlar için PI değerini artırın ve `double` kullanın.

---

## C — Küçük ipuçları

1) `scanf` dönüş değerini daima kontrol edin; hatalı girişleri yakalamak programın güvenliğini artırır.  
2) Değişken isimlerini anlamlı seçin (`base`, `height`, `radius`).  
3) Ondalık hesaplarda doğruluk için `double` tercih edin.  
4) Sabitleri `#define` ile tanımlamak kodu okunur kılar; ihtiyaç varsa `const` değişken de kullanılabilir.
