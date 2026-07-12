# Variables and Basic I/O

Level: Beginner
Purpose: Introduce variables in C, reading input from the user (scanf), printing output (printf), basic data types and simple arithmetic.

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. If you use a Windows compiler that provides them, you can add those calls. On GCC (Linux/macOS), `conio.h` is not available; use `getchar()` for pauses or remove those lines.

---

This page covers
- Basic data types: int, float, double, char
- Using printf and scanf, format specifiers
- Type conversions (casting) and integer vs. floating division
- Constants and macros (`#define`)
- Examples: triangle area, rectangle perimeter, circle circumference and area

---

## A — Core concepts

### 1) Data types
- `int`: integers (e.g. 1, -5, 100)
- `float` / `double`: floating-point numbers (e.g. 3.14). `double` gives higher precision.
- `char`: single character (e.g. 'a')
- Example declarations: `int n; float x; char c;`

### 2) printf and scanf
- Use `printf` to print to the screen; format specifiers: `%d` (int), `%f` (float), `%lf` (double), `%c` (char).
- Use `scanf` to read from the user; always check the return value:
  - `if (scanf("%d", &n) != 1) return 1;`

### 3) Type conversions (casting)
- `int / int` performs integer division; for a fractional result make at least one operand `float`/`double` or use a cast:
  - Example: `area = (float)(base * height) / 2.0f;`

### 4) Constants and macros
- Define constants with `#define PI 3.14f`. For more precision use more digits or `double`.
- Alternative: `const double pi = 3.141592653589793;`

---

## B — Examples and explanations

Each example: Goal → Explanation → Code → Important notes

### Example 1 — Triangle area
Goal: Variable declaration, user input, casting for correct division.

Explanation: Area = (base * height) / 2. If `base` and `height` are integers a cast is needed for a fractional result.

Code:
```c
#include <stdio.h>

int main(void) {
    int base, height;
    float area;

    printf("Enter base length: ");
    if (scanf("%d", &base) != 1) return 1;

    printf("Enter height: ");
    if (scanf("%d", &height) != 1) return 1;

    area = (float)(base * height) / 2.0f;
    printf("Area: %.2f\n", area);

    return 0;
}
```

Important note:
- Without the `(float)` cast integer division may produce an unexpected result.

### Example 2 — Rectangle perimeter
Goal: Use `float` and basic arithmetic.

Explanation: Perimeter = 2 * (side1 + side2) or `2*side1 + 2*side2`.

Code:
```c
#include <stdio.h>

int main(void) {
    float side1, side2, perimeter;

    printf("Enter first side: ");
    if (scanf("%f", &side1) != 1) return 1;

    printf("Enter second side: ");
    if (scanf("%f", &side2) != 1) return 1;

    perimeter = 2.0f * side1 + 2.0f * side2;
    printf("Perimeter: %.2f\n", perimeter);

    return 0;
}
```

Important note:
- `float` supports fractional side lengths; use `double` for more precision.

### Example 3 — Circle circumference and area
Goal: Use a macro for a constant and compute multiple values.

Explanation: Circumference = 2 * PI * r, Area = PI * r * r.

Code:
```c
#include <stdio.h>
#define PI 3.14f

int main(void) {
    float radius, circumference, area;

    printf("Enter radius: ");
    if (scanf("%f", &radius) != 1) return 1;

    circumference = 2.0f * PI * radius;
    area = PI * radius * radius;

    printf("Circumference: %.2f | Area: %.2f\n", circumference, area);

    return 0;
}
```

Important note:
- Use a more precise PI and `double` for higher accuracy.

---

## C — Small tips

1) Always check `scanf` return value to handle invalid input and make your program safer.
2) Choose clear variable names (`base`, `height`, `radius`).
3) Use `double` for decimal calculations when accuracy matters.
4) Defining constants with `#define` improves readability; use `const` if appropriate.
