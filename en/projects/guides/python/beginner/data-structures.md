# Data Structures (List, Tuple, Set, Dict)

This page covers common basic data structures in Python: list, tuple, set, and dictionary. For each structure, the page explains when to use it, how to create and use it, and practical examples.

You will learn
- Lists: dynamic collections, add/remove, slicing
- Tuple: immutable sequences and use cases
- Set: unique elements and set operations (intersection, union, difference)
- Dict: key-value storage and common patterns (`get`, `items`, `keys`, `values`)

---

## Lists

Lists hold ordered collections and can contain mixed types. You can add, remove, and slice lists.

Create and basic operations
```python
# create list
fruits = ['apple', 'pear', 'banana']
# add
fruits.append('cherry')
fruits.insert(1, 'strawberry')
# remove
fruits.remove('pear')
last = fruits.pop()
# access and slice
first = fruits[0]
last_two = fruits[-2:]
print(fruits)
```

Slicing
```python
nums = [0,1,2,3,4,5]
print(nums[1:4])   # [1,2,3]
print(nums[:3])    # [0,1,2]
print(nums[::2])   # [0,2,4]
```

Note: lists are mutable. `b = a` makes a reference; copy with `a.copy()` or `list(a)`.

---

## Tuple

Tuple is an immutable sequence. Use it for fixed collections, multiple return values, or dictionary keys.

Example
```python
coord = (10.0, 20.0)
# coord[0] = 5  # TypeError
x, y = coord  # unpacking
```

Tuples are useful for small, fixed datasets.

---

## Set

Sets contain unique elements. Useful for deduplication and set operations.

Create and basic operations
```python
s = {1, 2, 3}
s.add(4)
s.discard(2)
A = {1,2,3}
B = {3,4,5}
print(A & B)  # intersection {3}
print(A | B)  # union {1,2,3,4,5}
print(A - B)  # difference {1,2}
```

Deduplicate
```python
items = [1,2,2,3,3,3]
unique = set(items)  # {1,2,3}
```

Note: sets are unordered and elements must be hashable.

---

## Dictionary (dict)

Dict stores key-value pairs. Use for fast key-based access. Keys must be hashable.

Basic usage
```python
person = {
    'name': 'Ali',
    'age': 30,
}
print(person['name'])        # KeyError if missing
print(person.get('job'))     # None if missing
person['age'] = 31
person['job'] = 'engineer'
for key, value in person.items():
    print(key, value)
```

Common methods: `dict.keys()`, `dict.values()`, `dict.items()`, `dict.get(key, default)`, `in` for key checks.

---

## Example: building a list (Beatles)
```python
beatles = []
beatles.append('John Lennon')
beatles.append('Paul McCartney')
beatles.append('George Harrison')
beatles.insert(1, 'Ringo Starr')
print('Beatles members:')
for member in beatles:
    print('-', member)
```

---

## Example: unique elements (set)
```python
items = [1, 2, 2, 4, 3, 3, 1]
unique_list = list(set(items))
print(unique_list)
```

Preserve order when deduplicating
```python
def unique_preserve_order(seq):
    seen = set()
    result = []
    for x in seq:
        if x not in seen:
            seen.add(x)
            result.append(x)
    return result
```

---

## Tips

- Choose data structures by access and update patterns: `dict`/`set` for fast membership, `list` for ordered access.
- Avoid unnecessary copies with large data.
- Use tuples for hashable fixed data.
