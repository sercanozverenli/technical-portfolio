# Matrisler ve 2B Veri Yapıları

Seviye: Başlangıç  
Amaç: C'de iki boyutlu diziler (matrisler) ile temel işlemler: tanımlama, erişim, toplam, çarpma, transpoz, skaler çarpma ve matris yazdırma.

Not (conio.h / getch): Bu rehberdeki kodlar platformlar arası çalışması için `conio.h` ve `getch()` çağrılarını içermez. Bekleme için `getchar()` veya ilgili satırları kaldırabilirsiniz.

---

Bu sayfada ele alınacak konular
- 2B dizi bildirimi ve erişim (row-major düzen)  
- Matris toplamı ve skaler çarpma  
- Matris transpoz (transpose)  
- Matris çarpımı (matrix multiplication)  
- Matrisleri güvenli şekilde okuyup yazma

---

## A — Temel kavramlar

- İki boyutlu dizi tanımı: `int a[rows][cols];`  
- Erişim: `a[i][j]` ile i'nci satır j'inci sütun elemanına ulaşılır (0 tabanlı indeksleme).  
- Satır-sütun düzeni: C satır-temelli (row-major) düzen kullanır; `a[1]` bir satıra işaret eden dizi gibidir.  
- Matris çarpımı için boyut uyumu: A(m x n) ile B(n x p) çarpılabilir ve sonuç C(m x p) olur.

---

## B — Örnek uygulamalar ve açıklamalar

### 1) Matris okuma ve yazdırma
Amaç: Matris boyutlarını okuyup elemanları girdikten sonra matrisin düzgün şekilde yazdırılması.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    printf("Satir sayisi (r) ve sutun sayisi (c) girin: ");
    if (scanf("%d %d", &r, &c) != 2 || r <= 0 || c <= 0) return 1;

    int a[r][c];
    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) {
            if (scanf("%d", &a[i][j]) != 1) return 1;
        }
    }

    printf("Matris (%dx%d):\n", r, c);
    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) printf("%5d", a[i][j]);
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- VLA (variable-length arrays) kullanan bu örnek C99 ve sonrası derleyicilerde çalışır; taşınabilirlik için sabit boyut veya dinamik bellek (malloc) tercih edilebilir.

---

### 2) Matris toplama
Amaç: Aynı boyuttaki iki matrisin toplanması.

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    if (scanf("%d %d", &r, &c) != 2) return 1;
    int A[r][c], B[r][c], C[r][c];

    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;
    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &B[i][j]) != 1) return 1;

    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j)
            C[i][j] = A[i][j] + B[i][j];

    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) printf("%5d", C[i][j]);
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- İşlem öncesi matris boyutlarının eşit olduğundan emin olun.

---

### 3) Matris transpoz (transpose)
Amaç: Bir matrisin transpozunu elde etme (satır-sütun yer değiştirir).

Kod örneği:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    if (scanf("%d %d", &r, &c) != 2) return 1;
    int A[r][c];
    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;

    printf("Transpose (%dx%d -> %dx%d):\n", r, c, c, r);
    for (int j = 0; j < c; ++j) {
        for (int i = 0; i < r; ++i) printf("%5d", A[i][j]);
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- Kare matrisler (r == c) için transpoz yerinde de yapılabilir; genel durumda yeni matris veya farklı yazdırma yaklaşımı kullanın.

---

### 4) Matris çarpımı
Amaç: A(m x n) ile B(n x p) matrislerinin çarpımını hesaplamak.

Kod örneği:
```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int m, n, p;
    if (scanf("%d %d %d", &m, &n, &p) != 3) return 1;

    int A[m][n];
    int B[n][p];
    int C[m][p];

    for (int i = 0; i < m; ++i)
        for (int j = 0; j < n; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;
    for (int i = 0; i < n; ++i)
        for (int j = 0; j < p; ++j) if (scanf("%d", &B[i][j]) != 1) return 1;

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < p; ++j) {
            long sum = 0;
            for (int k = 0; k < n; ++k) sum += (long)A[i][k] * B[k][j];
            C[i][j] = (int)sum;
        }
    }

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < p; ++j) printf("%8d", C[i][j]);
        printf("\n");
    }
    return 0;
}
```

Önemli not:
- İç döngü üçlü (i,j,k) olduğundan zaman karmaşıklığı O(m*n*p) olur; büyük matrislerde performans ve taşma (overflow) açısından dikkatli olun.

---

## C — Küçük ipuçları

1) VLA kullanımı derleyicinizin C99/C11 desteğine bağlıdır; taşınabilirlik için dinamik bellek yöntemlerini öğrenin.  
2) Matris çarpımında ara toplam için `long` kullanmak taşma riskini azaltır; yine de büyük değerlerde dikkatli olun.  
3) Matris boyutlarını kullanıcıdan alırken geçersiz veya aşırı büyük girişleri reddedin.  
4) Matris işlemlerini fonksiyonlara ayırmak kodu okunur ve test edilebilir kılar.
