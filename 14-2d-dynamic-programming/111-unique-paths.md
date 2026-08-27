# 111. Unique Paths

## Problem

You are standing at the top-left corner of an `m x n` grid. You can only move right or down. Count how many distinct paths lead to the bottom-right corner. Assume `1 <= m, n <= 100`.

## Example 1

### Input

```text
m = 3, n = 7
```

### Expected Output

```text
28
```

### Explanation

There are 28 distinct combinations of right/down moves that get you from the top-left to the bottom-right of a 3x7 grid.

## Example 2

### Input

```text
m = 3, n = 2
```

### Expected Output

```text
3
```

### Explanation

The three paths are: down-down-right, down-right-down, right-down-down.

## How to Solve

### Key Idea

The number of ways to reach any cell equals the ways to reach the cell above it plus the ways to reach the cell to its left, since those are the only two directions you can arrive from.

### Approach

1. Create a 2D table `dp` of size `m x n`.
2. Set the first row and first column to all 1s, since there is only one way to reach any of those cells (keep moving in one direction).
3. Fill the rest with `dp[i][j] = dp[i-1][j] + dp[i][j-1]`.
4. Return `dp[m-1][n-1]`.

### Why It Works

Every path to cell `(i, j)` must come from either directly above or directly to the left, so adding the path counts from those two neighbors gives the total ways to reach `(i, j)`.

## Visual Explanation

```text
dp table for m = 3, n = 7 (rows = i, columns = j):

       j=0  j=1  j=2  j=3  j=4  j=5  j=6
 i=0    1    1    1    1    1    1    1
 i=1    1    2    3    4    5    6    7
 i=2    1    3    6   10   15   21   28

dp[i][j] = dp[i-1][j] + dp[i][j-1]
dp[2][6] = dp[1][6] + dp[2][5] = 7 + 21 = 28  <- matches Example 1's output
```

## Optimal Solution

```python
class Solution:
    def uniquePaths(self, m: int, n: int) -> int:
        dp = [[1] * n for _ in range(m)]

        for i in range(1, m):
            for j in range(1, n):
                dp[i][j] = dp[i - 1][j] + dp[i][j - 1]

        return dp[m - 1][n - 1]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n) (can be reduced to O(n) with a rolling row)

## Real-World Uses

- Counting distinct routes for a delivery robot restricted to grid movements.
- Estimating combinatorial possibilities in warehouse or maze layouts.
- Modeling lattice-path problems in probability and combinatorics.

## Key Takeaway

This is a classic grid DP pattern: each cell's answer is built from the cells above and to the left, which is the foundation for many other grid-path problems.
