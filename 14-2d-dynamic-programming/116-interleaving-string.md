# 116. Interleaving String

## Problem

Given three strings `s1`, `s2`, and `s3`, determine if `s3` can be formed by interleaving `s1` and `s2`. Interleaving means combining the characters of `s1` and `s2` while keeping each string's original character order, mixed together in any pattern.

## Example 1

### Input

```text
s1 = "aabcc", s2 = "dbbca", s3 = "aadbbcbcac"
```

### Expected Output

```text
true
```

### Explanation

You can interleave "aabcc" and "dbbca" character by character (preserving order within each) to exactly form "aadbbcbcac".

## Example 2

### Input

```text
s1 = "aabcc", s2 = "dbbca", s3 = "aadbbbaccc"
```

### Expected Output

```text
false
```

### Explanation

No interleaving of "aabcc" and "dbbca" preserving order can produce "aadbbbaccc".

## How to Solve

### Key Idea

Use a 2D grid where `dp[i][j]` means "the first `i` characters of `s1` and first `j` characters of `s2` can interleave to form the first `i+j` characters of `s3`." Fill it in based on whether the last character came from `s1` or `s2`.

### Approach

1. If `len(s1) + len(s2) != len(s3)`, return False immediately.
2. Create `dp` of size `(len(s1)+1) x (len(s2)+1)`, with `dp[0][0] = True`.
3. Fill the first row and column using only characters from `s2` or only from `s1` respectively.
4. For each `dp[i][j]`, it is True if (`s1[i-1] == s3[i+j-1]` and `dp[i-1][j]`) or (`s2[j-1] == s3[i+j-1]` and `dp[i][j-1]`).
5. Return `dp[len(s1)][len(s2)]`.

### Why It Works

At each step, the next character of `s3` must come from either the current position in `s1` or in `s2`. Checking both possibilities and combining them with OR captures every valid way to build up the interleaving.

## Visual Explanation

```text
s1 = "aabcc" (rows), s2 = "dbbca" (cols), s3 = "aadbbcbcac"

dp[i][j] = true if the first i chars of s1 and first j chars of s2 interleave into the first i+j chars of s3

        ""   d   b   b   c   a
  ""     T   F   F   F   F   F
  a      T   F   F   F   F   F
  a      T   T   T   T   T   F
  b      F   T   T   F   T   F
  c      F   F   T   T   T   T
  c      F   F   F   T   F   T

dp[5][5] = True  <- matches Example 1's output
```

## Optimal Solution

```python
class Solution:
    def isInterleave(self, s1: str, s2: str, s3: str) -> bool:
        m, n = len(s1), len(s2)
        if m + n != len(s3):
            return False

        dp = [[False] * (n + 1) for _ in range(m + 1)]
        dp[0][0] = True

        for i in range(m + 1):
            for j in range(n + 1):
                if i == 0 and j == 0:
                    continue
                from_s1 = i > 0 and dp[i - 1][j] and s1[i - 1] == s3[i + j - 1]
                from_s2 = j > 0 and dp[i][j - 1] and s2[j - 1] == s3[i + j - 1]
                dp[i][j] = from_s1 or from_s2

        return dp[m][n]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n) (can be reduced to O(n))

## Real-World Uses

- Verifying that merged log streams from two sources preserve each source's original order.
- Validating that a shuffled deck or merged data feed interleaves two known sequences correctly.
- Checking that a merge-sort style combination of two lists could have produced a given output.

## Key Takeaway

When merging two sequences into a third while preserving order, a 2D DP grid indexed by how much of each source sequence has been used is the natural way to track feasibility.
