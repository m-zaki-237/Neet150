# 117. Longest Increasing Path in a Matrix

## Problem

Given an `m x n` integer matrix, find the length of the longest path where each next cell (moving up, down, left, or right) has a strictly greater value than the current one. You may start from any cell.

## Example 1

### Input

```text
matrix = [[9,9,4],[6,6,8],[2,1,1]]
```

### Expected Output

```text
4
```

### Explanation

The longest increasing path is 1 -> 2 -> 6 -> 9.

## Example 2

### Input

```text
matrix = [[3,4,5],[3,2,6],[2,2,1]]
```

### Expected Output

```text
4
```

### Explanation

The longest increasing path is 3 -> 4 -> 5 -> 6.

## How to Solve

### Key Idea

From each cell, the longest increasing path is 1 plus the best result among neighbors with a strictly larger value. Cache each cell's answer so it is computed only once, turning an exponential search into a fast 2D DP via DFS + memoization.

### Approach

1. Create a 2D memo table `memo` of size `m x n`, initialized to 0 (meaning "not computed").
2. For each cell, run a DFS: look at all 4 neighbors with a strictly greater value, recurse into them, and take the max result.
3. The answer for the current cell is `1 + max(neighbor results)`, or just `1` if no neighbor qualifies.
4. Store the result in `memo` before returning so repeated calls are instant.
5. Run this DFS from every cell and return the overall maximum.

### Why It Works

Because paths must strictly increase, there are no cycles, so each cell's best answer depends only on cells with strictly greater values — this guarantees memoization is safe and every cell's result is computed exactly once.

## Visual Explanation

```text
matrix:              memo (longest increasing path starting at each cell):
 9  9  4               1  1  2
 6  6  8               2  2  1
 2  1  1               3  4  2

memo[r][c] = 1 + max(memo of strictly-greater neighbors), or 1 if none qualify
memo[2][1] (value 1) = 1 + max(memo[1][1]=2, memo[2][0]=3) = 4  <- the answer, path 1 -> 2 -> 6 -> 9
```

## Optimal Solution

```python
from typing import List
from functools import lru_cache

class Solution:
    def longestIncreasingPath(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])

        @lru_cache(maxsize=None)
        def dfs(r: int, c: int) -> int:
            best = 1
            for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                nr, nc = r + dr, c + dc
                if 0 <= nr < rows and 0 <= nc < cols and matrix[nr][nc] > matrix[r][c]:
                    best = max(best, 1 + dfs(nr, nc))
            return best

        return max(dfs(r, c) for r in range(rows) for c in range(cols))
```

## Complexity

- **Time:** O(m * n) since each cell is computed once
- **Space:** O(m * n) for the memo table and recursion stack

## Real-World Uses

- Finding the longest chain of increasing elevation in terrain/height-map data.
- Analyzing dependency graphs on a grid where values must strictly increase (e.g. skill trees).
- Optimizing traversal order in image processing where pixel intensity must strictly increase.

## Key Takeaway

DFS plus memoization on a grid is a powerful pattern for path problems with a natural ordering constraint (like strictly increasing values) that prevents cycles.
