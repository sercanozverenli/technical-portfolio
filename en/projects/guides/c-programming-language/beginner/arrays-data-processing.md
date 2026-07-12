# Arrays and Data Processing

Level: Beginner
Purpose: Basic data processing with one-dimensional arrays: reading/writing elements, sum/average, min/max, search, reverse and rotate operations.

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. If you use a Windows compiler that provides them, you can add those calls. On GCC (Linux/macOS), `conio.h` is not available; use `getchar()` for pauses or remove those lines.

---

This page covers
- Declaring and reading/writing one-dimensional arrays
- Array sum and average
- Finding minimum / maximum
- Linear search
- Reverse and rotate operations on arrays

---

## A — Core concepts

- Declare arrays like `int a[10];` for fixed-size arrays.
- Access elements with `a[i]` (0-based indexing).
- Pass array length explicitly to functions; do not rely on implicit sizes.

---

## B — Examples and explanations

### 1) Array sum and sum of squares / average
Goal: Compute the sum, sum of squares and arithmetic mean of array elements.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    printf("Array size n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) {
        printf("Enter element %d: ", i + 1);
        if (scanf("%d", &a[i]) != 1) return 1;
    }

    long sum = 0;
    long sum_squares = 0;
    for (i = 0; i < n; ++i) {
        sum += a[i];
        sum_squares += (long)a[i] * a[i];
    }

    printf("Sum = %ld\n", sum);
    printf("Sum of squares = %ld\n", sum_squares);
    printf("Average = %.2f\n", (double)sum / n);
    return 0;
}
```

Important note:
- Use `long` for sums to reduce overflow risk.

---

### 2) Find min / max
Goal: Find minimum and maximum values in an array.

Code:
```c
#include <stdio.h>
#include <limits.h>

int main(void) {
    int n, i;
    printf("Array size n: ");
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

Important note:
- INT_MAX/INT_MIN help detect bounds and avoid overflow.

---

### 3) Linear search
Goal: Check if a value exists in an array and return its first position.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i, target;
    printf("Array size n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) {
        if (scanf("%d", &a[i]) != 1) return 1;
    }

    printf("Enter the value to search: ");
    if (scanf("%d", &target) != 1) return 1;

    int pos = -1;
    for (i = 0; i < n; ++i) {
        if (a[i] == target) { pos = i; break; }
    }

    if (pos >= 0) printf("Value found, index = %d\n", pos);
    else printf("Value not in array.\n");
    return 0;
}
```

Important note:
- For frequent searches consider sorting and using binary search for better performance.

---

### 4) Reverse an array (in-place)
Goal: Reverse array elements in-place.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    printf("Array size n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (i = 0; i < n; ++i) if (scanf("%d", &a[i]) != 1) return 1;

    for (i = 0; i < n/2; ++i) {
        int tmp = a[i];
        a[i] = a[n - 1 - i];
        a[n - 1 - i] = tmp;
    }

    printf("Reversed array: ");
    for (i = 0; i < n; ++i) printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```

Important note:
- In-place algorithm uses minimal extra memory.

---

### 5) Rotate array to the right by k
Goal: Rotate array to the right by `k` positions using a simple O(n*k) method.

Code:
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
    printf("Array size n: ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    int a[n];
    for (int i = 0; i < n; ++i) if (scanf("%d", &a[i]) != 1) return 1;

    printf("Enter k (rotate right): ");
    if (scanf("%d", &k) != 1) return 1;

    rotate_right(a, n, k);

    printf("Result: ");
    for (int i = 0; i < n; ++i) printf("%d ", a[i]);
    printf("\n");
    return 0;
}
```

Important note:
- This simple method is O(n*k); for large `k`, prefer O(n) algorithms (reverse trick).

---

## C — Small tips

1) Pass array size to functions explicitly; do not rely on implicit sizes.
2) Watch memory limits; use dynamic allocation (`malloc`) for large arrays.
3) Remember 0-based indexing; off-by-one errors are common.
4) If searches are frequent consider sorting and using binary search.
