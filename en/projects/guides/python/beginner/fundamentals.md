# Python Fundamentals

This page explains Python basics for beginners in clear English. Each section first describes the concept, then provides short and explanatory Python examples.

What you will learn on this page
- Number types: differences between int and float, limitations and conversions.
- Operators: arithmetic operators, exponentiation, modulus and division behavior.
- Input and output: input(), print(), f-strings and handling user input.
- Functions: simple function definitions and why to use them.
- Error handling and validation: try/except and safe user input.
- Practical tips.

---

## Number types: int and float

Python commonly uses two numeric types: integers (`int`) and floating-point numbers (`float`). Key differences:
- `int`: whole numbers (e.g. -3, 0, 42).
- `float`: numbers with fractional parts (e.g. 3.14, -0.5). Floating point has precision and representation limits.

Examples
```python
# int example
a = 5  # int
b = 2  # int

# float example
x = 2.5  # float
y = 0.1  # float
```

Division behavior
- `/` always returns a float (in Python 3).
- `//` is floor division and yields an integer-like result for many inputs.

```python
print(5 / 2)   # 2.5
print(5 // 2)  # 2
print(-3 // 2) # -2 (floor behavior)
```

Floating point operations can show small rounding errors. For high-precision needs consider the `decimal` module or specialized libraries.

---

## Operators

Common arithmetic operators:
- Addition: `+`
- Subtraction: `-`
- Multiplication: `*`
- Division: `/`
- Floor division: `//`
- Modulus: `%`
- Exponentiation: `**`

Operator precedence and parentheses
- `**` has high precedence.
- Use parentheses `()` to change evaluation order.

Examples
```python
print(2 + 3 * 4)    # 14, multiplication before addition
print((2 + 3) * 4)  # 20, parentheses change order
print(3 ** 2)       # 9, exponentiation
print(10 % 3)       # 1, modulus
```

A practical example using operators: computing the hypotenuse.

### Example: Hypotenuse calculation
Compute the hypotenuse from two legs: square each leg, sum, then take square root.

```python
# Hypotenuse calculator: read two legs from the user and validate
from math import sqrt

try:
    a = float(input("Enter length of first leg: "))
    b = float(input("Enter length of second leg: "))
except ValueError:
    print("Invalid input: please enter numeric values.")
else:
    if a <= 0 or b <= 0:
        print("Lengths must be positive.")
    else:
        sum_sq = a ** 2 + b ** 2
        c = sqrt(sum_sq)
        print(f"Calculated hypotenuse: {c}")
```

This example shows `float()` conversion, `**` for power, `math.sqrt`, and simple validation with try/except and if.

---

## Input and output (input / print)

- `input(prompt)` reads text from the user and returns a string.
- Convert strings to numbers with `int()` or `float()`.
- Use `print()` to output; format with f-strings (Python 3.6+).

Example: simple addition
```python
try:
    s1 = float(input("Enter a number: "))
    s2 = float(input("Enter another number: "))
except ValueError:
    print("Invalid input: please enter numbers.")
else:
    total = s1 + s2
    print(f"The sum is: {total}")
```

f-string example
```python
name = "Ayse"
score = 93.4567
print(f"{name}'s score: {score:.2f}")  # .2f limits decimals
```

---

## Functions: why and how

Functions let you split code into reusable pieces. Use `def` to define a function; inputs are parameters, output via `return`.

Example: simple conversion function
```python
GALLON_TO_LITERS = 3.78541

def gallons_to_liters(gallons):
    """Convert gallons to liters."""
    return gallons * GALLON_TO_LITERS

print(gallons_to_liters(2))
```

Benefits: reuse, readability, and easier testing.

---

## Error handling and input validation

User input is external and can be invalid. Use `try/except` to handle errors and ask again when needed.

Retry example
```python
def read_positive_float(prompt):
    while True:
        try:
            value = float(input(prompt))
        except ValueError:
            print("Invalid input. Please enter a numeric value.")
            continue
        if value <= 0:
            print("Please enter a positive value.")
            continue
        return value

# usage
 a = read_positive_float("Enter first leg: ")
 b = read_positive_float("Enter second leg: ")
```

This prevents unexpected failures and improves user experience.

---

## Tips and things to watch for

- Use meaningful variable names (in examples short names are used; in real code prefer descriptive names).
- Floating point precision: consider `decimal` for financial calculations.
- Use the `math` module for common math functions like `math.sqrt` or `math.pi`.
- For repeated or large computations, algorithm choice matters.
