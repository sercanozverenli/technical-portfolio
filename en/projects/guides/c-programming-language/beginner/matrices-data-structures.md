# Matrices and 2D Data Structures

Level: Beginner
Purpose: Basics of two-dimensional arrays (matrices) in C: declaration, access, addition, multiplication, transpose, scalar multiply, and printing matrices.

Note (conio.h / getch): Examples do not use `conio.h` or `getch()` for portability. Use `getchar()` for pauses or remove those lines.

---

This page covers
- 2D array declaration and access (row-major order)
- Matrix addition and scalar multiply
- Matrix transpose
- Matrix multiplication
- Reading and printing matrices safely

---

## A — Core concepts

- 2D array declaration: `int a[rows][cols];`
- Access: `a[i][j]` accesses row `i`, column `j` (0-based indexing).
- Row-major layout: C stores rows contiguously; `a[1]` behaves like an array representing the second row.
- Matrix multiplication dimension rule: A(m x n) times B(n x p) yields C(m x p).

---

## B — Examples and explanations

### 1) Read and print a matrix
Goal: Read matrix dimensions and elements, then print the matrix.

Code:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    printf("Enter number of rows (r) and columns (c): ");
    if (scanf("%d %d", &r, &c) != 2 || r <= 0 || c <= 0) return 1;

    int a[r][c];
    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) {
            if (scanf("%d", &a[i][j]) != 1) return 1;
        }
    }

    printf("Matrix (%dx%d):\n", r, c);
    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) printf("%5d", a[i][j]);
        printf("\n");
    }
    return 0;
}
```

Important note:
- This example uses VLA (variable-length arrays) which require C99 or later. For portability use fixed sizes or dynamic memory (`malloc`).

---

### 2) Matrix addition
Goal: Add two matrices of the same size.

Code:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    if (scanf("%d %d", &r, &c) != 2) return 1;
    int A[r][c], B[r][c], C[r][c];

    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;
    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &B[i][j]) != 1) return 1;

    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j)
            C[i][j] = A[i][j] + B[i][j];

    for (int i = 0; i < r; ++i) {
        for (int j = 0; j < c; ++j) printf("%5d", C[i][j]);
        printf("\n");
    }
    return 0;
}
```

Important note:
- Ensure matrices have the same dimensions before adding.

---

### 3) Matrix transpose
Goal: Compute the transpose of a matrix (swap rows and columns).

Code:
```c
#include <stdio.h>

int main(void) {
    int r, c;
    if (scanf("%d %d", &r, &c) != 2) return 1;
    int A[r][c];
    for (int i = 0; i < r; ++i)
        for (int j = 0; j < c; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;

    printf("Transpose (%dx%d -> %dx%d):\n", r, c, c, r);
    for (int j = 0; j < c; ++j) {
        for (int i = 0; i < r; ++i) printf("%5d", A[i][j]);
        printf("\n");
    }
    return 0;
}
```

Important note:
- For square matrices (r == c) transpose can be done in-place; for general matrices print into a new matrix or use a different approach.

---

### 4) Matrix multiplication
Goal: Multiply A(m x n) by B(n x p).

Code:
```c
#include <stdio.h>
#include <stdlib.h>

int main(void) {
    int m, n, p;
    if (scanf("%d %d %d", &m, &n, &p) != 3) return 1;

    int A[m][n];
    int B[n][p];
    int C[m][p];

    for (int i = 0; i < m; ++i)
        for (int j = 0; j < n; ++j) if (scanf("%d", &A[i][j]) != 1) return 1;
    for (int i = 0; i < n; ++i)
        for (int j = 0; j < p; ++j) if (scanf("%d", &B[i][j]) != 1) return 1;

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < p; ++j) {
            long sum = 0;
            for (int k = 0; k < n; ++k) sum += (long)A[i][k] * B[k][j];
            C[i][j] = (int)sum;
        }
    }

    for (int i = 0; i < m; ++i) {
        for (int j = 0; j < p; ++j) printf("%8d", C[i][j]);
        printf("\n");
    }
    return 0;
}
```

Important note:
- The triple loop (i,j,k) leads to O(m*n*p) time; watch performance and overflow for large matrices.

---

## C — Small tips

1) VLA usage depends on compiler support (C99/C11); learn dynamic memory methods for portability.
2) Use `long` for intermediate sums in matrix multiplication to reduce overflow risk.
3) Validate matrix dimensions from user input to avoid excessively large allocations.
4) Split matrix operations into functions for readability and testability.
