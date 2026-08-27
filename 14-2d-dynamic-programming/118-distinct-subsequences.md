# 118. Distinct Subsequences

## Problem

Given two strings `s` and `t`, count how many distinct subsequences of `s` equal `t`. A subsequence is formed by deleting some (possibly zero) characters without changing the order of the remaining ones.

## Example 1

### Input

```text
s = "rabbbit", t = "rabbit"
```

### Expected Output

```text
3
```

### Explanation

There are 3 ways to pick characters from "rabbbit" (choosing which "b" to skip) that spell out "rabbit".

## Example 2

### Input

```text
s = "babgbag", t = "bag"
```

### Expected Output

```text
5
```

### Explanation

There are 5 different ways to choose characters from "babgbag" that spell "bag".

## How to Solve

### Key Idea

Build a 2D table where `dp[i][j]` is the number of ways the first `i` characters of `s` can form the first `j` characters of `t`. Each character of `s` either gets used to match the current character of `t` or gets skipped.

### Approach

1. Create `dp` of size `(len(s)+1) x (len(t)+1)`.
2. Set `dp[i][0] = 1` for all `i` (an empty `t` is always formed once, by deleting everything).
3. For each `i, j` (both starting at 1), start with `dp[i][j] = dp[i-1][j]` (skip `s[i-1]`).
4. If `s[i-1] == t[j-1]`, add `dp[i-1][j-1]` (use `s[i-1]` to match `t[j-1]`).
5. Return `dp[len(s)][len(t)]`.

### Why It Works

Every way to form `t[:j]` from `s[:i]` either ignores the last character of `s` (carrying over the count from one row up) or uses it to match the last character of `t` when they are equal, so summing both possibilities counts every distinct way exactly once.

## Visual Explanation

```text
s = "rabbbit" (rows), t = "rabbit" (cols)

            ""  r  a  b  b  i  t
    ""       1  0  0  0  0  0  0
    r        1  1  0  0  0  0  0
    ra       1  1  1  0  0  0  0
    rab      1  1  1  1  0  0  0
    rabb     1  1  1  2  1  0  0
    rabbb    1  1  1  3  3  0  0
    rabbbi   1  1  1  3  3  3  0
    rabbbit  1  1  1  3  3  3  3

dp[i][j] = dp[i-1][j] + (dp[i-1][j-1] if s[i-1] == t[j-1] else 0)
The three b's in s each add a way to match "bb" in t, growing the count 1 -> 2 -> 3 down the "b" column.
dp[7][6] = 3  <- matches Example 1's output
```

## Optimal Solution

```python
class Solution:
    def numDistinct(self, s: str, t: str) -> int:
        m, n = len(s), len(t)
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(m + 1):
            dp[i][0] = 1

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                dp[i][j] = dp[i - 1][j]
                if s[i - 1] == t[j - 1]:
                    dp[i][j] += dp[i - 1][j - 1]

        return dp[m][n]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n) (can be reduced to O(n))

## Real-World Uses

- Counting distinct ways a search term can be spelled out from a larger text (fuzzy search ranking).
- Bioinformatics counting of how many ways a target sequence occurs as a subsequence of a genome.
- Analytics on how many distinct user click-paths match a target funnel sequence.

## Key Takeaway

Counting subsequence matches is a variant of the LCS-style grid DP, where matching characters add extra ways instead of just extending a single best length.
