# Kontrol Akışı (Conditionals & Branching)

Seviye: Başlangıç  
Amaç: C'de karar yapıları (if / else), karşılaştırma ve mantıksal operatörleri, dallanma ile problemleri nasıl çözeceğinizi öğrenmek.

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Kodları kendi ortamınızda kullanmak isterseniz:
- Windows'ta bazı derleyiciler `conio.h` ve `getch()` sağlar; bu durumda ilgili satırları ekleyebilirsiniz.
- GCC (Linux/macOS) ve çoğu modern derleyicide `conio.h` yoktur; bekleme için `getchar()` kullanabilir veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- Karşılaştırma operatörleri: `==`, `!=`, `>`, `<`, `>=`, `<=`  
- Mantıksal operatörler: `&&`, `||`, `!`  
- `if`, `if-else`, `if-else if-else` yapıları  
- İç içe (nested) koşullar ve kısa devre değerlendirme (short-circuit)  
- Yaygın hatalar ve nasıl önlenir: bölme sıfır kontrolü, kayan nokta karşılaştırması

---

## A — Temel kavramlar

### 1) Karşılaştırma operatörleri
- `==` eşit mi?  
- `!=` eşit değil mi?  
- `>` büyük mü? `<` küçük mü? `>=`, `<=` (büyük/eşit, küçük/eşit)

Örnek: `if (a > b)` — `a` büyüktür `b` ise giriş bloğu çalışır.

### 2) Mantıksal operatörler
- `&&` (VE): Her iki ifade de doğruysa sonuç doğru.  
- `||` (VEYA): En az bir ifade doğruysa sonuç doğru.  
- `!` (DEĞİL): İfadenin doğruluğunu tersine çevirir.

Kısa devre (short-circuit): `if (ptr != NULL && ptr->x > 0)` ifadesinde `ptr != NULL` yanlışsa ikinci koşul değerlendirilmez — bu güvenli kullandır.

### 3) if — else yapısı
- Basit örnek:
  ```c
  if (condition) {
      /* true ise burası */
  } else {
      /* false ise burası */
  }
  ```
- Birden çok durumu `else if` ile zincirleyebilirsiniz.

---

## B — Örnek uygulamalar ve açıklamalar

Her örnekte: Amaç → Açıklama → Kod örneği → Önemli notlar

### Örnek 1 — Dört sayı arasından en büyük sayıyı bulma
Amaç: Karşılaştırma operatörleri ve çoklu dallanma kullanımı.

Açıklama: Kullanıcıdan dört adet sayı alınır; en büyük sayıyı bulmak için basit `if-else` zinciri veya adım adım karşılaştırma kullanılabilir. Aşağıdaki kod basit if/else karşılaştırmalarıyla çalışır.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    float n1, n2, n3, n4, greatest;

    printf("Birinci sayıyı girin: ");
    if (scanf("%f", &n1) != 1) return 1;

    printf("İkinci sayıyı girin: ");
    if (scanf("%f", &n2) != 1) return 1;

    printf("Üçüncü sayıyı girin: ");
    if (scanf("%f", &n3) != 1) return 1;

    printf("Dördüncü sayıyı girin: ");
    if (scanf("%f", &n4) != 1) return 1;

    greatest = n1;

    if (n2 > greatest) greatest = n2;
    if (n3 > greatest) greatest = n3;
    if (n4 > greatest) greatest = n4;

    printf("En büyük sayı: %.2f\n", greatest);

    return 0;
}
```

Önemli not:
- Bu yaklaşım okunaklı ve hataya dayanıklıdır. Alternatif olarak `if-else` zinciri veya bir dizi kullanıp döngü ile maksimum bulma da uygundur.

### Örnek 2 — İkinci dereceden denklemin köklerini bulma (ax² + bx + c = 0)
Amaç: Mantıksal koşullar ile farklı durumları (iki gerçek kök, tek kök, gerçek kök yok) ayırma.

Açıklama: Diskriminant `D = b*b - 4*a*c` hesaplanır.  
- Eğer `D > 0`: iki farklı gerçek kök.  
- Eğer `D == 0`: çakışık (tekrarlı) kök.  
- Eğer `D < 0`: gerçek kök yok (karmaşık kökler).

Kod örneği:
```c
#include <stdio.h>
#include <math.h>

int main(void) {
    double a, b, c;
    double discriminant, x1, x2;

    printf("a katsayısını girin: ");
    if (scanf("%lf", &a) != 1) return 1;

    printf("b katsayısını girin: ");
    if (scanf("%lf", &b) != 1) return 1;

    printf("c katsayısını girin: ");
    if (scanf("%lf", &c) != 1) return 1;

    if (a == 0.0) {
        /* a == 0 ise ikinci dereceden denklem değil */
        if (b == 0.0) {
            printf("Denklem sabit bir değer veya geçersiz.\n");
        } else {
            /* bx + c = 0 -> x = -c / b */
            printf("Tek kök (lineer): x = %.6f\n", -c / b);
        }
        return 0;
    }

    discriminant = b * b - 4.0 * a * c;

    if (discriminant > 0.0) {
        x1 = (-b - sqrt(discriminant)) / (2.0 * a);
        x2 = (-b + sqrt(discriminant)) / (2.0 * a);
        printf("İki farklı gerçek kök: x1 = %.6f, x2 = %.6f\n", x1, x2);
    } else if (discriminant == 0.0) {
        x1 = -b / (2.0 * a);
        printf("Çakışık (tekrarlı) kök: x = %.6f\n", x1);
    } else {
        printf("Gerçek kök yok (karmaşık kökler).\n");
    }

    return 0;
}
```

Önemli not:
- `a` sıfır ise ikinci dereceden denklem tanımlı değildir; bu durumda lineer denklem olarak ele alınmalıdır.  
- Kayan nokta (`double`) karşılaştırmalarında `==` kullanımı hassasiyete bağlı hatalara açıktır; üretimde tolerans (örn. `fabs(discriminant) < 1e-12`) kullanmak daha güvenlidir.

---

## C — Küçük ipuçları

1) Bölme yapmadan önce bölenin sıfır olmadığını kontrol edin (`if (b != 0) ...`).  
2) Çoklu koşullarda kısa devre mantığını kullanarak güvenli kontroller yazın (`ptr != NULL && ptr->val > 0`).  
3) Kayan nokta sayılarının doğrudan `==` ile karşılaştırılmasından kaçının; küçük toleranslar kullanın.  
4) Karşılaştırma zincirlerinde parantezlerle önceliği netleştirmek okunurluğu artırır (örn. `if ((a > b && a > c) || a == 100)`).
