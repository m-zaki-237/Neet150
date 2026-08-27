# 119. Edit Distance

## Problem

Given two strings `word1` and `word2`, find the minimum number of operations to convert `word1` into `word2`. You may insert a character, delete a character, or replace a character, each counting as one operation.

## Example 1

### Input

```text
word1 = "horse", word2 = "ros"
```

### Expected Output

```text
3
```

### Explanation

horse -> rorse (replace 'h' with 'r'), rorse -> rose (delete 'r'), rose -> ros (delete 'e'). Three operations.

## Example 2

### Input

```text
word1 = "intention", word2 = "execution"
```

### Expected Output

```text
5
```

### Explanation

Five operations (a mix of replace, insert, delete) are needed to transform "intention" into "execution".

## How to Solve

### Key Idea

Build a 2D table where `dp[i][j]` is the minimum edits to turn the first `i` characters of `word1` into the first `j` characters of `word2`. Compare the last characters to decide whether an operation is needed.

### Approach

1. Create `dp` of size `(len(word1)+1) x (len(word2)+1)`.
2. Set `dp[i][0] = i` (delete all `i` characters) and `dp[0][j] = j` (insert all `j` characters).
3. If `word1[i-1] == word2[j-1]`, set `dp[i][j] = dp[i-1][j-1]` (no operation needed).
4. Otherwise `dp[i][j] = 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])` covering delete, insert, and replace.
5. Return `dp[len(word1)][len(word2)]`.

### Why It Works

Any transformation of a prefix pair can be broken down into the last operation performed (insert, delete, replace, or match), and each of those corresponds directly to one neighboring cell in the table, so taking the best (minimum) among them gives the optimal number of edits.

## Visual Explanation

```text
word1 = "horse" (rows), word2 = "ros" (cols)

        ""  r  o  s
   ""    0  1  2  3
   h     1  1  2  3
   o     2  2  1  2
   r     3  2  2  2
   s     4  3  3  2
   e     5  4  4  3

dp[i][j] = dp[i-1][j-1] if word1[i-1] == word2[j-1], else 1 + min(dp[i-1][j], dp[i][j-1], dp[i-1][j-1])
dp[5][3] = 3  <- matches Example 1's output
```

## Optimal Solution

```python
class Solution:
    def minDistance(self, word1: str, word2: str) -> int:
        m, n = len(word1), len(word2)
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(m + 1):
            dp[i][0] = i
        for j in range(n + 1):
            dp[0][j] = j

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if word1[i - 1] == word2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                else:
                    dp[i][j] = 1 + min(dp[i - 1][j], dp[i][j - 1], dp[i - 1][j - 1])

        return dp[m][n]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n) (can be reduced to O(n))

## Real-World Uses

- Spell-checkers and autocorrect computing the closest matching word.
- File diff/merge tools quantifying how different two versions of text are.
- DNA sequence alignment scoring in bioinformatics.

## Key Takeaway

Edit distance is the classic 2D DP for string transformation: each cell combines the three neighboring cells (up, left, diagonal) to represent delete, insert, and replace/match operations.
