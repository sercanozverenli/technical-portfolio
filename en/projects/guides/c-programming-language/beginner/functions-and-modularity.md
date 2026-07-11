# Functions and Modularity

Level: Beginner
Purpose: Define and use functions in C, parameter and return types, make code modular, and common example functions (power, factorial, combinations, series).

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. If you use a Windows compiler that provides them, you can add those calls. On GCC (Linux/macOS), `conio.h` is not available; use `getchar()` for pauses or remove those lines.

---

This page covers
- Function declaration (prototype) and definition
- Parameters, return types and local variables
- Modular code: extracting repeated code to functions
- Example functions: power, factorial, combination (nCr), e^x series, cosine series

---

## A — Core concepts

### 1) Function declaration and definition
- A declaration (prototype) states the function signature: `float power(int x, int n);`
- A definition provides the function body:
  ```c
  float power(int x, int n) {
      float result = 1.0f;
      for (int i = 0; i < n; ++i) result *= x;
      return result;
  }
  ```
- Call from `main`: `s = power(2, 3);`

### 2) Parameters and return type
- Parameters are passed by value.
- The return value is the function's result and returned with `return`.
- Local variables exist only within the function and disappear when it returns.

### 3) Modularity
- Move repeated logic into functions to reduce duplication and improve readability.
- Functions should be small and have a single responsibility.

---

## B — Example functions and explanations

### Example 1 — Power function
Goal: Implement a function to compute a number raised to an integer power.

Explanation: `power(x, n)` multiplies `x` by itself `n` times.

Code:
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
    printf("Enter base (x): ");
    if (scanf("%d", &x) != 1) return 1;
    printf("Enter exponent (n): ");
    if (scanf("%d", &n) != 1) return 1;

    float s = power(x, n);
    printf("%d to the power of %d = %.2f\n", x, n, s);
    return 0;
}
```

Important note:
- This simple function only handles positive integer exponents; extend it for negative powers or fractional bases if needed.

---

### Example 2 — Factorial and combination (nCr)
Goal: Compute factorial and combinations showing function reuse.

Explanation: `factorial(k)` computes `k!`. Combination `C(n,r) = n! / (r! * (n-r)!)`.

Code:
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
    printf("Enter n: ");
    if (scanf("%d", &n) != 1) return 1;
    printf("Enter r: ");
    if (scanf("%d", &r) != 1) return 1;

    if (r < 0 || r > n) {
        printf("Invalid r value.\n");
        return 1;
    }

    double comb = factorial(n) / (factorial(r) * factorial(n - r));
    printf("Combination C(%d,%d) = %.0f\n", n, r, comb);
    return 0;
}
```

Important note:
- Factorials grow quickly; for large `n` prefer safer methods or iterative combination calculation.

---

### Example 3 — e^x calculation using series
Goal: Use functions to compute e^x via Taylor series.

Explanation: e^x ≈ 1 + x/1! + x^2/2! + ... + x^n/n!. We can reuse `power` and `factorial`.

Code:
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
    printf("Enter number of series terms (n): ");
    if (scanf("%d", &n) != 1) return 1;
    printf("Enter x value: ");
    if (scanf("%d", &x) != 1) return 1;

    double e = 1.0;
    for (int i = 1; i <= n; ++i) {
        e += power(x, i) / factorial(i);
    }
    printf("e to the power %d (approx) = %f\n", x, e);
    return 0;
}
```

Important note:
- `power` here is written for `int` base; for general use change base to `double`.

---

### Example 4 — Cosine calculation using series
Goal: Compute cosine using series, demonstrating functions and loops.

Explanation: cos(x) ≈ 1 - x^2/2! + x^4/4! - ... Use a `sign` variable to alternate signs.

Code:
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
    printf("Enter number of series terms (n): ");
    if (scanf("%d", &n) != 1) return 1;
    printf("Enter x value (in radians): ");
    if (scanf("%d", &x) != 1) return 1;

    double cosine = 1.0;
    int sign = -1;
    for (int i = 2; i <= 2 * n; i += 2) {
        cosine += sign * power(x, i) / factorial(i);
        sign = -sign;
    }
    printf("Cosine(%d) (approx) = %f\n", x, cosine);
    return 0;
}
```

Important note:
- `x` should be in radians for series approximation; `power` and `factorial` can have overflow/performance issues for large `n`.

---

## C — Small tips

1) Keep functions small and single-purpose for easier testing and reuse.
2) Put function prototypes in headers and definitions in separate files for larger projects.
3) Remember arguments are passed by value; use pointers for large data structures.
4) For recursion (e.g. factorial) be aware of stack limits; iterative approaches are often safer.
