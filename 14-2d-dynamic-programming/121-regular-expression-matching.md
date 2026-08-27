# 121. Regular Expression Matching

## Problem

Given a string `s` and a pattern `p`, determine if `p` matches the entire string `s`. The pattern can contain regular lowercase letters, `.` (matches any single character), and `*` (matches zero or more of the preceding character or `.`).

## Example 1

### Input

```text
s = "aa", p = "a*"
```

### Expected Output

```text
true
```

### Explanation

"a*" means zero or more of "a", so it can match "aa" by repeating "a" twice.

## Example 2

### Input

```text
s = "mississippi", p = "mis*is*p*."
```

### Expected Output

```text
false
```

### Explanation

The pattern cannot stretch to cover "mississippi" exactly, so there is no match.

## How to Solve

### Key Idea

Build a 2D table where `dp[i][j]` means "the first `i` characters of `s` match the first `j` characters of `p`." Handle `*` specially since it can represent zero occurrences (skip two pattern characters) or one more occurrence (consume one `s` character and stay on the same pattern position).

### Approach

1. Create `dp` of size `(len(s)+1) x (len(p)+1)`, with `dp[0][0] = True` (empty matches empty).
2. Fill `dp[0][j]` for patterns like `a*b*c*` that can match an empty string, by checking if `p[j-1] == '*'` and `dp[0][j-2]` is True.
3. For each `i, j` (both >= 1): if `p[j-1]` is `.` or equals `s[i-1]`, set `dp[i][j] = dp[i-1][j-1]`.
4. If `p[j-1] == '*'`, set `dp[i][j] = dp[i][j-2]` (zero occurrences) OR, if `p[j-2]` matches `s[i-1]`, also OR in `dp[i-1][j]` (one more occurrence).
5. Return `dp[len(s)][len(p)]`.

### Why It Works

Every matching decision reduces to a smaller prefix pair, and the `*` case correctly branches between "use zero copies of the previous pattern character" and "use one more copy," covering every way the star could be expanded.

## Visual Explanation

```text
s = "aa" (rows), p = "a*" (cols)

        ""  a  *
   ""    T  F  T
   a     F  T  T
   a     F  F  T

if p[j-1] is '.' or matches s[i-1]: dp[i][j] = dp[i-1][j-1]
if p[j-1] == '*': dp[i][j] = dp[i][j-2] (zero occurrences), OR dp[i-1][j] if the preceding pattern char matches s[i-1] (one more occurrence)
dp[2][2] = True  <- matches Example 1's output ("a*" matches "aa" by repeating 'a')
```

## Optimal Solution

```python
class Solution:
    def isMatch(self, s: str, p: str) -> bool:
        m, n = len(s), len(p)
        dp = [[False] * (n + 1) for _ in range(m + 1)]
        dp[0][0] = True

        for j in range(1, n + 1):
            if p[j - 1] == '*' and j >= 2:
                dp[0][j] = dp[0][j - 2]

        for i in range(1, m + 1):
            for j in range(1, n + 1):
                if p[j - 1] == '.' or p[j - 1] == s[i - 1]:
                    dp[i][j] = dp[i - 1][j - 1]
                elif p[j - 1] == '*':
                    dp[i][j] = dp[i][j - 2]
                    prev = p[j - 2]
                    if prev == '.' or prev == s[i - 1]:
                        dp[i][j] = dp[i][j] or dp[i - 1][j]

        return dp[m][n]
```

## Complexity

- **Time:** O(m * n)
- **Space:** O(m * n)

## Real-World Uses

- Regex engines used inside text editors and IDEs for search-and-replace.
- Input validation and log parsing systems that match flexible patterns.
- Search engines and command-line tools (like grep) supporting wildcard pattern matching.

## Key Takeaway

Pattern matching with wildcards is a 2D DP over string prefixes, where the trickiest part is correctly branching on `*` between "skip it" and "reuse the preceding character one more time."
