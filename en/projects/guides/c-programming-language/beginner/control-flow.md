# Control Flow (Conditionals & Branching)

Level: Beginner
Purpose: Learn decision structures in C (if / else), comparison and logical operators, and how to solve problems with branching.

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. If you use a Windows compiler that provides them, you can add those calls. On GCC (Linux/macOS), `conio.h` is not available; use `getchar()` for pauses or remove those lines.

---

This page covers
- Comparison operators: `==`, `!=`, `>`, `<`, `>=`, `<=`
- Logical operators: `&&`, `||`, `!`
- `if`, `if-else`, `if-else if-else` structures
- Nested conditions and short-circuit evaluation
- Common pitfalls: division by zero checks, floating-point comparisons

---

## A — Core concepts

### 1) Comparison operators
- `==` equal?
- `!=` not equal?
- `>` greater than? `<` less than? `>=`, `<=` (greater-or-equal, less-or-equal)

Example: `if (a > b)` — the block runs if `a` is greater than `b`.

### 2) Logical operators
- `&&` (AND): true if both expressions are true.
- `||` (OR): true if at least one expression is true.
- `!` (NOT): negates the expression.

Short-circuit: In `if (ptr != NULL && ptr->x > 0)` if `ptr != NULL` is false the second condition is not evaluated — this is safe usage.

### 3) if — else structure
- Simple example:
  ```c
  if (condition) {
      /* runs if true */
  } else {
      /* runs if false */
  }
  ```
- Use `else if` to chain multiple cases.

---

## B — Examples and explanations

Each example: Goal → Explanation → Code → Important notes

### Example 1 — Find the largest of four numbers
Goal: Use comparison operators and multiple branching.

Explanation: Read four numbers from the user; find the largest using simple comparisons or an `if-else` chain. The code below uses step-by-step comparisons.

Code:
```c
#include <stdio.h>

int main(void) {
    float n1, n2, n3, n4, greatest;

    printf("Enter first number: ");
    if (scanf("%f", &n1) != 1) return 1;

    printf("Enter second number: ");
    if (scanf("%f", &n2) != 1) return 1;

    printf("Enter third number: ");
    if (scanf("%f", &n3) != 1) return 1;

    printf("Enter fourth number: ");
    if (scanf("%f", &n4) != 1) return 1;

    greatest = n1;

    if (n2 > greatest) greatest = n2;
    if (n3 > greatest) greatest = n3;
    if (n4 > greatest) greatest = n4;

    printf("Greatest number: %.2f\n", greatest);

    return 0;
}
```

Important note:
- This approach is readable and robust. Alternatively use an `if-else` chain or put values in an array and loop to find the maximum.

### Example 2 — Quadratic equation roots (ax² + bx + c = 0)
Goal: Use logical conditions to separate cases (two real roots, one root, no real roots).

Explanation: Compute discriminant `D = b*b - 4*a*c`.
- If `D > 0`: two distinct real roots.
- If `D == 0`: repeated root.
- If `D < 0`: no real roots (complex roots).

Code:
```c
#include <stdio.h>
#include <math.h>

int main(void) {
    double a, b, c;
    double discriminant, x1, x2;

    printf("Enter coefficient a: ");
    if (scanf("%lf", &a) != 1) return 1;

    printf("Enter coefficient b: ");
    if (scanf("%lf", &b) != 1) return 1;

    printf("Enter coefficient c: ");
    if (scanf("%lf", &c) != 1) return 1;

    if (a == 0.0) {
        /* not a quadratic equation if a == 0 */
        if (b == 0.0) {
            printf("Equation is a constant or invalid.\n");
        } else {
            /* bx + c = 0 -> x = -c / b */
            printf("Single root (linear): x = %.6f\n", -c / b);
        }
        return 0;
    }

    discriminant = b * b - 4.0 * a * c;

    if (discriminant > 0.0) {
        x1 = (-b - sqrt(discriminant)) / (2.0 * a);
        x2 = (-b + sqrt(discriminant)) / (2.0 * a);
        printf("Two distinct real roots: x1 = %.6f, x2 = %.6f\n", x1, x2);
    } else if (discriminant == 0.0) {
        x1 = -b / (2.0 * a);
        printf("Repeated root: x = %.6f\n", x1);
    } else {
        printf("No real roots (complex roots).\n");
    }

    return 0;
}
```

Important note:
- If `a` is zero it is not a quadratic equation; handle it as a linear equation. For floating-point comparisons prefer a tolerance like `fabs(discriminant) < 1e-12` in production.

---

## C — Small tips

1) Check divisors are not zero before dividing (`if (b != 0) ...`).
2) Use short-circuit logic for safe checks (`ptr != NULL && ptr->val > 0`).
3) Avoid direct `==` comparisons with floating-point numbers; use small tolerances.
4) Use parentheses in complex comparison expressions to clarify precedence (e.g. `if ((a > b && a > c) || a == 100)`).
