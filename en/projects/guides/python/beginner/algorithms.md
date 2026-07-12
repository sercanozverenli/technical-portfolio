# Basic Algorithms and Complexity

This page explains basic algorithms and complexity in simple English with small examples. The format is topic-first: explain the idea, then show example code and practical notes.

You will learn
- What an algorithm is and why it matters
- Time complexity (Big O) basics
- Sorting: bubble sort example and optimization
- Searching: linear search vs binary search
- Practical tips on choosing algorithms

---

## What is an algorithm and why it matters

An algorithm is a sequence of steps to solve a specific problem. Different algorithms can solve the same problem with different speed and memory usage. For large data, the right algorithm is important.

---

## Time complexity (Big O)

Time complexity describes how runtime grows with input size `n`.
- O(1): constant time
- O(log n): logarithmic (e.g. binary search)
- O(n): linear
- O(n log n): good sorting algorithms (e.g. mergesort)
- O(n^2): quadratic (e.g. simple sorts)

Small `n` may tolerate O(n^2) but larger `n` will not.

---

## Sorting: Bubble Sort

Bubble sort is a simple sorting algorithm: repeatedly compare adjacent elements and swap if out of order. It has O(n^2) complexity.

Simple implementation
```python
def bubble_sort(a):
    n = len(a)
    for i in range(n):
        for j in range(0, n - i - 1):
            if a[j] > a[j + 1]:
                a[j], a[j + 1] = a[j + 1], a[j]

arr = [5, 2, 9, 1, 5]
bubble_sort(arr)
print(arr)  # [1, 2, 5, 5, 9]
```

Optimized version stops early if no swaps happen in a pass.

```python
def bubble_sort_optimized(a):
    n = len(a)
    for i in range(n):
        swapped = False
        for j in range(0, n - i - 1):
            if a[j] > a[j + 1]:
                a[j], a[j + 1] = a[j + 1], a[j]
                swapped = True
        if not swapped:
            break
```

---

## Searching: Linear vs Binary

Linear search checks each element and is O(n). It works on unsorted lists.

```python
def linear_search(a, target):
    for i, val in enumerate(a):
        if val == target:
            return i
    return -1
```

Binary search works on sorted lists and is O(log n).

```python
def binary_search(a, target):
    left, right = 0, len(a) - 1
    while left <= right:
        mid = (left + right) // 2
        if a[mid] == target:
            return mid
        elif a[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return -1

arr = [1, 2, 5, 7, 9]
print(binary_search(arr, 7))  # 3
```

Choose linear search for unsorted single checks, or sort once and use binary search for many lookups.

---

## Practical tips

- For small lists simple algorithms are fine; use Python's built-in `sorted()` for general sorting.
- Consider both time and space complexity.
- Choose data structures (set/dict) when membership tests are frequent.
