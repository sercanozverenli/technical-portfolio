# Loops and Iteration

Level: Beginner
Purpose: Learn loop constructs in C (`for` / `while`), nested loops, producing patterns with loops and basic algorithmic thinking.

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. If you use a Windows compiler that provides them, you can add those calls. On GCC (Linux/macOS), `conio.h` is not available; use `getchar()` for pauses or remove those lines.

---

This page covers
- `for` and `while` loops
- Nested loops
- Loop counters and condition checks
- Creating patterns with loops
- Common pitfalls (infinite loops, counter types)

---

## A — Core concepts

### 1) for loop
- Structure: `for (init; condition; increment) { /* ... */ }`
- Typical use: a known number of iterations.

### 2) while loop
- Structure: `while (condition) { /* ... */ }`
- Typical use: repeat while a condition holds; use when the number of iterations is not known beforehand.

### 3) Nested loops
- Inner loop runs from the start for each iteration of the outer loop; used for matrices and pattern generation.
- Complexity: two nested loops have O(n*m) time.

### 4) Loop errors
- Infinite loop: condition never becomes false.
- Counter type: wrong type for counter can cause overflow or incorrect results (e.g., using `float` instead of `int`).

---

## B — Examples and explanations

Each example: Goal → Explanation → Code → Important notes

### Example 1 — Sum of 1..n and sum of squares
Goal: Use a `for` loop to sum numbers and their squares.

Explanation: Read `n` from the user; compute sums from 1 to `n`.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i;
    long sum = 0;
    long sum_squares = 0;

    printf("Enter a positive integer (n): ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 1; i <= n; ++i) {
        sum += i;
        sum_squares += i * i;
    }

    printf("Sum of numbers 1..%d = %ld\n", n, sum);
    printf("Sum of squares 1..%d = %ld\n", n, sum_squares);

    return 0;
}
```

Important note:
- Use `long` for sums to reduce overflow risk.

---

### Example 2 — Average calculation
Goal: Read `n` numbers in a loop and compute their average.

Explanation: User specifies how many numbers; loop reads each and accumulates a total to compute the average.

Code:
```c
#include <stdio.h>

int main(void) {
    int i, n;
    double total = 0.0, number;

    printf("How many numbers will you enter? ");
    if (scanf("%d", &n) != 1 || n <= 0) return 1;

    for (i = 0; i < n; ++i) {
        printf("Enter number %d: ", i + 1);
        if (scanf("%lf", &number) != 1) return 1;
        total += number;
    }

    printf("Average = %.2f\n", total / n);
    return 0;
}
```

Important note:
- Use `double` for `total` and `number` to support fractional input.

---

### Example 3 — Multiplication by repeated addition
Goal: Reinforce loops by computing multiplication as repeated addition.

Explanation: Instead of using `a * b` directly, add `b` to a result `a` times. Handle negatives and decimals separately if needed.

Code:
```c
#include <stdio.h>

int main(void) {
    int a, b, i;
    long result = 0;

    printf("Enter multiplier (a) (positive integer): ");
    if (scanf("%d", &a) != 1 || a < 0) return 1;

    printf("Enter multiplicand (b) (integer): ");
    if (scanf("%d", &b) != 1) return 1;

    for (i = 0; i < a; ++i) {
        result += b;
    }

    printf("Result = %ld\n", result);
    return 0;
}
```

Important note:
- Extend the algorithm for negative or fractional numbers as needed.

---

### Example 4 — 5x5 multiplication table (each cell i*j + 1)
Goal: Demonstrate nested `for` loops to produce a table-like output.

Explanation: Use two loops (row and column) and compute `(i * j) + 1` for each cell.

Code:
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

Important note:
- Proper formatting keeps output readable with nested loops.

---

### Example 5 — Reverse number triangle
Goal: Generate a reverse-number triangle using nested loops.

Explanation: Outer loop sets the number of rows; inner loop prints numbers in descending order for each row.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i, j;

    printf("Enter number of rows: ");
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

Important note:
- The inner loop starts at `i` because it counts down.

---

### Example 6 — Pyramid star pattern
Goal: Print a centered pyramid using spaces and stars.

Explanation: For each row print leading spaces, then `2*i - 1` stars.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, i, j;

    printf("Enter number of rows: ");
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

Important note:
- Output may break in small terminal windows; use a monospace font and sufficient width for proper alignment.

---

### Example 7 — Digit sum and reverse
Goal: Process digits of a number using a `while` loop, using modulo and division.

Explanation: Extract each digit with `n % 10`; compute `reverse = reverse * 10 + digit` and `sum += digit`.

Code:
```c
#include <stdio.h>

int main(void) {
    int n, temp, digit;
    int sum = 0;
    int reverse = 0;

    printf("Enter an integer: ");
    if (scanf("%d", &n) != 1) return 1;

    temp = n;
    if (temp < 0) temp = -temp; /* handle negative numbers if needed */

    while (temp > 0) {
        digit = temp % 10;
        sum += digit;
        reverse = reverse * 10 + digit;
        temp = temp / 10;
    }

    printf("Digit sum = %d\n", sum);
    printf("Reversed number = %d\n", reverse);
    return 0;
}
```

Important note:
- Add logic if you need to preserve sign for negative numbers.

---

## C — Small tips

1) Carefully set loop bounds; `<=` vs `<` can cause off-by-one bugs.
2) Nested loops can be O(N^2); consider alternatives for large inputs.
3) Move expensive computations outside the loop when possible.
4) Avoid infinite loops like `for (;;)` unless intentional and ensure exit conditions.
