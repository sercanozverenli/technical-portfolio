# Loops and Iteration

This page covers how to use loops in Python (`for` and `while`), common patterns, and data processing examples. Each section explains the concept first and then gives reinforcing code examples.

What you will learn
- Basic use of `for` and counting with `range()`
- Iterating over collections (lists, strings, tuples)
- `while` loops and stop conditions
- Loop control with `break`, `continue`, and `else`
- `enumerate()` and `zip()` for index and parallel iteration
- List comprehensions for compact, readable code
- Common examples: character counting, filtering, pattern printing, Collatz sequence

---

## `for` loop: basic usage

`for` iterates over a collection item by item:
```python
for item in collection:
    # do something with item
```

Example: print items of a list
```python
fruits = ['apple', 'pear', 'banana']
for fruit in fruits:
    print(fruit)
```

Counting with `range()`
```python
for i in range(5):         # 0,1,2,3,4
    print(i)
for i in range(1, 10, 2):  # 1,3,5,7,9
    print(i)
```

---

## Iteration over sequences: string, list, tuple

Strings are iterable too; you can loop character by character.
```python
s = 'hello'
for ch in s:
    print(ch)
```

Use `enumerate()` to iterate with an index:
```python
lst = ['a', 'b', 'c']
for i, val in enumerate(lst, start=1):
    print(i, val)  # 1 a, 2 b, 3 c
```

Use `zip()` for parallel iteration:
```python
names = ['Ali', 'Ayse']
scores = [80, 95]
for name, score in zip(names, scores):
    print(f"{name}: {score}")
```

---

## `while` loop: condition-based iteration

`while` runs as long as a condition is True. Ensure the loop will terminate, otherwise it may run forever.

Example: count from 1 to 5
```python
i = 1
while i <= 5:
    print(i)
    i += 1
```

---

## Loop control: `break`, `continue`, `else`

- `break` stops the loop immediately.
- `continue` skips to the next iteration.
- `for/while ... else`: the `else` block runs if the loop finished normally (no `break`).

Example: search until found
```python
nums = [3, 7, 10, 2]
for n in nums:
    if n == 10:
        print('Found:', n)
        break
else:
    print('Not found')
```

---

## List comprehensions

A compact way to create lists:
```python
[<expr> for <item> in <iterable> if <cond>]
```

Examples
```python
# even numbers from 1 to 10
evens = [i for i in range(1, 11) if i % 2 == 0]
# squares
squares = [x**2 for x in [1, 2, 3, 4]]
```

---

## Common examples and patterns

### Count characters (example: count 's' in "Mississippi")
```python
s = 'Mississippi'
count = 0
for ch in s:
    if ch.lower() == 's':
        count += 1
print('s count:', count)
```

---

### Remove vowels from a string
```python
text = 'Merhaba Dünya'
vowels = 'aeıioöuüAEIİOÖUÜ'
result = ''.join([ch for ch in text if ch not in vowels])
print(result)
```

---

### Pyramid pattern (simple example)
```python
height = 4
for i in range(1, height + 1):
    print(' ' * (height - i) + '*' * (2 * i - 1))
```

---

### Collatz sequence (while loop example)
```python
n = int(input('Enter a starting positive integer: '))
steps = 0
while n != 1:
    if n % 2 == 0:
        n = n // 2
    else:
        n = 3 * n + 1
    steps += 1
print('Total steps:', steps)
```

---

## Tips and cautions

- Avoid unnecessary copies in loops when working with large data.
- Ensure `while` loops have a stop condition or a max iteration guard.
- Prefer `enumerate()` and `zip()` for clearer code when you need indices or parallel iteration.
