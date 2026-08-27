# 112. Longest Common Subsequence

## Problem

Given two strings `text1` and `text2`, find the length of their longest common subsequence. A subsequence keeps the original order of characters but can skip characters. If there is no common subsequence, return 0.

## Example 1

### Input

```text
text1 = "abcde", text2 = "ace"
```

### Expected Output

```text
3
```

### Explanation

"ace" is a subsequence of both strings and has length 3, which is the longest possible.

## Example 2

### Input

```text
text1 = "abc", text2 = "def"
```

### Expected Output

```text
0
```

### Explanation

The two strings share no characters, so there is no common subsequence.

## How to Solve

### Key Idea

Compare the strings character by character using a 2D table: if the current characters match, extend the diagonal answer by one; otherwise take the best answer from skipping a character in either string.

### Approach

1. Build a table `dp` of size `(len(text1)+1) x (len(text2)+1)`, initialized to 0.
2. For each `i, j`, if `text1[i-1] == text2[j-1]`, set `dp[i][j] = dp[i-1][j-1] + 1`.
3. Otherwise set `dp[i][j] = max(dp[i-1][j], dp[i][j-1])`.
4. Return `dp[len(text1)][len(text2)]`.

### Why It Works

`dp[i][j]` represents the LCS length using the first `i` characters of `text1` and first `j` characters of `text2`. Matching characters extend the best previous match, and non-matching characters just carry forward the best result from dropping one character.

## Visual Explanation

```text
dp table for text1 = "abcde", text2 = "ace":

       ""  a  c  e
  ""    0  0  0  0
  a     0  1  1  1
  b     0  1  1  1
  c     0  1  2  2
  d     0  1  2  2
  e     0  1  2  3

dp[i][j] = dp[i-1][j-1] + 1 if text1[i-1] == text2[j-1], else max(dp[i-1][j], dp[i][j-1])
e.g. dp[3][2] ('c' == 'c'): dp[2][1] + 1 = 1 + 1 = 2
dp[5][3] = 3  <- matches Example 1's output
```

## Optimal Solution

```python
class Solution:
    def longestCommonSubsequence(self, text1: str, text2: str) -> int:
        m, n = len(text1), len(text2)
        dp = [[0] * (n + 1) for _ in range(m + 1)]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if text1[i - 1] == text2[j - 1]:
                    dp[i][j] = dp[i - 1][j - 1] + 1
                else:
                    dp[i][j] = max(dp[i - 1][j], dp[i][j - 1])

        return dp[m][n]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n) (can be reduced to O(n))

## Real-World Uses

- Version-control diff tools comparing file revisions line by line.
- DNA/protein sequence alignment in bioinformatics.
- Plagiarism and similarity detection between documents.

## Key Takeaway

LCS is the template problem for 2D string DP: build a grid where rows and columns represent prefixes of each string, and let matches vs. mismatches decide how to fill each cell.
