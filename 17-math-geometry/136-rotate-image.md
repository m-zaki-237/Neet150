# 136. Rotate Image

## Problem

You are given an `n x n` 2D matrix representing an image. Rotate the image by 90 degrees clockwise. You must do this **in place**, meaning you cannot use another matrix to hold the rotated result.

## Example 1

### Input

```text
matrix = [[1,2,3],
          [4,5,6],
          [7,8,9]]
```

### Expected Output

```text
[[7,4,1],
 [8,5,2],
 [9,6,3]]
```

### Explanation

Each layer of the matrix is rotated so that the first column becomes the first row (reversed).

## Example 2

### Input

```text
matrix = [[1,2],
          [3,4]]
```

### Expected Output

```text
[[3,1],
 [4,2]]
```

### Explanation

For a 2x2 matrix, the left column becomes the top row.

## How to Solve

### Key Idea

Rotating 90 degrees clockwise can be done by first flipping the matrix over its diagonal (transpose), then reversing each row.

### Approach

1. Transpose the matrix: swap `matrix[i][j]` with `matrix[j][i]` for all `i < j`.
2. Reverse each row of the transposed matrix.
3. This produces the matrix rotated 90 degrees clockwise, all done in place.

### Why It Works

Transposing turns rows into columns. Reversing each row after that flips the columns left-to-right, which together has the same effect as rotating the whole grid clockwise.

## Visual Explanation

```text
Transpose:              Reverse each row:
1 2 3        1 4 7      1 4 7        7 4 1
4 5 6   ->   2 5 8  ->  2 5 8   ->   8 5 2
7 8 9        3 6 9      3 6 9        9 6 3
```

## Optimal Solution

```python
class Solution:
    def rotate(self, matrix: list[list[int]]) -> None:
        n = len(matrix)

        # Transpose the matrix
        for i in range(n):
            for j in range(i + 1, n):
                matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]

        # Reverse each row
        for row in matrix:
            row.reverse()
```

## Complexity

- **Time:** O(n^2)
- **Space:** O(1) extra space (in place)

## Real-World Uses

- Photo-editing apps and mobile camera apps rotating images to correct orientation.
- Image-processing pipelines rotating pixel grids/tensors before feeding them into computer-vision models.
- Puzzle and tile-based games rotating grid pieces in place.

## Key Takeaway

Transpose + reverse rows is a classic trick for in-place matrix rotation. Recognizing that a rotation can be broken into two simpler, well-known operations avoids extra memory.
