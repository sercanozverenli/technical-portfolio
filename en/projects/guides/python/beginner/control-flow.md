# Conditions and Decision Structures

This page explains how to write conditions in Python, comparison and logical operators, `if/elif/else` structures, and practical examples. Each section first explains the concept and then shows clear code examples.

What you will learn
- Comparison operators (`==`, `!=`, `>`, `<`, `>=`, `<=`)
- Logical operators (`and`, `or`, `not`) and short-circuit behavior
- `if / elif / else` structure and good usage patterns
- Nested conditions and safe checks
- Two practical examples: leap year check and event end-time calculation

---

## Comparison operators

Comparison operators compare two values and return `True` or `False`. Common operators:
- `==` : equal?
- `!=` : not equal?
- `>`  : greater than?
- `<`  : less than?
- `>=` : greater or equal?
- `<=` : less or equal?

Examples
```python
print(5 == 5)   # True
print(5 != 3)   # True
print(4 > 7)    # False
print(3 <= 3)   # True
```

Notes
- Don't confuse `==` (comparison) with `=` (assignment).
- Comparisons work for strings, lists, etc. (e.g. `'a' < 'b'`).

---

## Logical operators: and, or, not

Logical operators combine conditions:
- `and`: True if both expressions are True.
- `or`: True if at least one expression is True.
- `not`: negates the expression.

Short-circuit behavior
- With `and`, if the left operand is False, the right side is not evaluated.
- With `or`, if the left operand is True, the right side is not evaluated.

Examples
```python
a = 10
b = 5
print(a > 0 and b > 0)   # True
print(a < 0 or b > 0)    # True
print(not (a == b))      # True
```

Use short-circuiting for safe checks, for example:
```python
if obj is not None and obj.value > 0:
    print("Value is positive")
```
If `obj` is `None`, the second expression is not evaluated and AttributeError is avoided.

---

## if / elif / else structure

Python conditional statements look like:
```python
if condition1:
    # runs if condition1 is True
elif condition2:
    # runs if condition2 is True
else:
    # runs if no condition above is True
```

Good practices
- If you need many `elif` branches, consider using a dictionary mapping for clarity.
- For simple choices, use a ternary expression: `x = 'positive' if n > 0 else 'not positive'`

Example: letter grade
```python
def letter_grade(score):
    if score >= 90:
        return 'A'
    elif score >= 80:
        return 'B'
    elif score >= 70:
        return 'C'
    elif score >= 60:
        return 'D'
    else:
        return 'F'

print(letter_grade(85))  # B
```

---

## Nested conditions and safety

Sometimes you need conditions inside other conditions. Avoid deep nesting by splitting logic into functions or using early returns.

Example: safe early return
```python
def process(value):
    if value is None:
        return
    if not isinstance(value, int):
        return
    # now value is a safe int
    print(value * 2)
```

---

## Example 1 — Leap year check

Rules:
- If year divisible by 400 -> leap year.
- Else if divisible by 100 -> not leap year.
- Else if divisible by 4 -> leap year.
- Otherwise not a leap year.

Code
```python
try:
    year = int(input("Enter year: "))
except ValueError:
    print("Invalid year")
else:
    if year % 400 == 0:
        print("Leap year")
    elif year % 100 == 0:
        print("Not a leap year")
    elif year % 4 == 0:
        print("Leap year")
    else:
        print("Not a leap year")
```

---

## Example 2 — Event end time calculation

Scenario: given a start time in HH:MM format and a duration in minutes, compute the end time in 24-hour format.

Code
```python
def parse_time(hhmm):
    """Convert "HH:MM" string to (hour, minute)."""
    parts = hhmm.split(":")
    if len(parts) != 2:
        raise ValueError("Time must be in HH:MM format")
    hour = int(parts[0])
    minute = int(parts[1])
    if not (0 <= hour < 24 and 0 <= minute < 60):
        raise ValueError("Invalid hour or minute")
    return hour, minute

try:
    start = input("Event start time (HH:MM): ")
    duration = int(input("Duration (minutes): "))
    h, m = parse_time(start)
except ValueError as e:
    print("Invalid input:", e)
else:
    total_minutes = h * 60 + m + duration
    end_hour = (total_minutes // 60) % 24
    end_minute = total_minutes % 60
    print(f"Event ends at: {end_hour:02d}:{end_minute:02d}")
```
