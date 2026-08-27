# 137. Spiral Matrix

## Problem

Given an `m x n` matrix, return all its elements in spiral order — starting from the top-left, moving right, then down, then left, then up, and continuing to spiral inward until every element has been visited.

## Example 1

### Input

```text
matrix = [[1,2,3],
          [4,5,6],
          [7,8,9]]
```

### Expected Output

```text
[1,2,3,6,9,8,7,4,5]
```

### Explanation

Go right across the top row, down the right column, left across the bottom row, up the left column, then spiral into the middle.

## Example 2

### Input

```text
matrix = [[1,2,3,4],
          [5,6,7,8],
          [9,10,11,12]]
```

### Expected Output

```text
[1,2,3,4,8,12,11,10,9,5,6,7]
```

### Explanation

Same spiral pattern applied to a non-square matrix.

## How to Solve

### Key Idea

Keep track of four boundaries (top, bottom, left, right) and shrink them as you traverse each edge of the current "ring" of the matrix.

### Approach

1. Set `top = 0`, `bottom = rows - 1`, `left = 0`, `right = cols - 1`.
2. Traverse left to right along `top` row, then increment `top`.
3. Traverse top to bottom along `right` column, then decrement `right`.
4. If `top <= bottom`, traverse right to left along `bottom` row, then decrement `bottom`.
5. If `left <= right`, traverse bottom to top along `left` column, then increment `left`.
6. Repeat while `top <= bottom` and `left <= right`.

### Why It Works

Each pass consumes one full edge of the current outer ring and shrinks the boundary inward, so every element gets visited exactly once in spiral order without needing extra visited-tracking.

## Visual Explanation

```text
1 -> 2 -> 3
          |
4 -> 5    6
|         |
7 <- 8 <- 9
```

## Optimal Solution

```python
class Solution:
    def spiralOrder(self, matrix: list[list[int]]) -> list[int]:
        result = []
        if not matrix or not matrix[0]:
            return result

        top, bottom = 0, len(matrix) - 1
        left, right = 0, len(matrix[0]) - 1

        while top <= bottom and left <= right:
            for col in range(left, right + 1):
                result.append(matrix[top][col])
            top += 1

            for row in range(top, bottom + 1):
                result.append(matrix[row][right])
            right -= 1

            if top <= bottom:
                for col in range(right, left - 1, -1):
                    result.append(matrix[bottom][col])
                bottom -= 1

            if left <= right:
                for row in range(bottom, top - 1, -1):
                    result.append(matrix[row][left])
                left += 1

        return result
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(1) extra space, not counting the output list

## Real-World Uses

- Rendering grid-based UI layouts or game boards in a spiral pattern.
- Image processing techniques that traverse pixels in spiral order (e.g. certain compression or scanning schemes).
- Printer/plotter path planning that sweeps a bounded area layer by layer.

## Key Takeaway

Boundary-shrinking with four pointers is the standard way to handle spiral or ring-based matrix traversal problems.
