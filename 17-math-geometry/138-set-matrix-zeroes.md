# 138. Set Matrix Zeroes

## Problem

Given an `m x n` matrix, if an element is 0, set its entire row and column to 0. Do this **in place**, and try to use as little extra memory as possible (ideally O(1) extra space).

## Example 1

### Input

```text
matrix = [[1,1,1],
          [1,0,1],
          [1,1,1]]
```

### Expected Output

```text
[[1,0,1],
 [0,0,0],
 [1,0,1]]
```

### Explanation

The single 0 at position (1,1) zeroes out its entire row and column.

## Example 2

### Input

```text
matrix = [[0,1,2,0],
          [3,4,5,2],
          [1,3,1,5]]
```

### Expected Output

```text
[[0,0,0,0],
 [0,4,5,0],
 [0,3,1,0]]
```

### Explanation

The 0s at (0,0) and (0,3) zero out row 0, column 0, and column 3.

## How to Solve

### Key Idea

Use the first row and first column of the matrix itself as markers to remember which rows and columns need to become zero, instead of allocating a separate set or array.

### Approach

1. Check separately whether the first row and first column originally contain any zero (store as booleans, since they double as markers).
2. Scan the rest of the matrix (from row 1, col 1 onward); if `matrix[i][j] == 0`, mark `matrix[i][0] = 0` and `matrix[0][j] = 0`.
3. Scan again from row 1, col 1 onward; if `matrix[i][0] == 0` or `matrix[0][j] == 0`, set `matrix[i][j] = 0`.
4. Finally, zero out the first row and/or first column if their original flags said they contained a zero.

### Why It Works

The first row and column can safely store "should this row/col be zeroed" flags because we only read them for marking before we overwrite them, and we handle their own zero-state separately at the end.

## Visual Explanation

```text
Step 1 - Original:      Step 2 - Mark using row 0/col 0:   Step 3 - Zero using markers:
1 1 1                   1 0 1   <- matrix[0][1] marked      1 0 1
1 0 1                   0 0 1   <- matrix[1][0] marked      0 0 0   (row1 marker zeros [1][2];
1 1 1                   1 1 1                               1 0 1    col1 marker zeros [2][1])

matrix[1][1] == 0, so we set matrix[1][0] = 0 and matrix[0][1] = 0 as markers,
then any cell whose row-marker or col-marker is 0 gets zeroed in the final pass.
```

## Optimal Solution

```python
class Solution:
    def setZeroes(self, matrix: list[list[int]]) -> None:
        rows, cols = len(matrix), len(matrix[0])
        first_row_has_zero = any(matrix[0][j] == 0 for j in range(cols))
        first_col_has_zero = any(matrix[i][0] == 0 for i in range(rows))

        # Use first row/col as markers
        for i in range(1, rows):
            for j in range(1, cols):
                if matrix[i][j] == 0:
                    matrix[i][0] = 0
                    matrix[0][j] = 0

        # Zero out cells based on markers
        for i in range(1, rows):
            for j in range(1, cols):
                if matrix[i][0] == 0 or matrix[0][j] == 0:
                    matrix[i][j] = 0

        if first_row_has_zero:
            for j in range(cols):
                matrix[0][j] = 0

        if first_col_has_zero:
            for i in range(rows):
                matrix[i][0] = 0
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(1) extra space (in place)

## Real-World Uses

- Spreadsheet applications clearing dependent rows/columns when a cell is invalidated.
- Image masking operations that blank out entire rows/columns around a flagged pixel.
- Database/table sanitization that nulls out related rows and columns based on a sentinel value.

## Key Takeaway

When you need extra bookkeeping space but want O(1), look for unused or reusable parts of the input itself (like the first row/column) to store markers.
