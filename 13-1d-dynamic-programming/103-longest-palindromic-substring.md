# 103. Longest Palindromic Substring

## Problem

Given a string `s`, find the longest contiguous substring that reads the same forward and backward (a palindrome). If there are multiple answers of the same length, any one of them is acceptable.

## Example 1

### Input

```text
s = "babad"
```

### Expected Output

```text
"bab"
```

### Explanation

"bab" and "aba" are both valid palindromes of length 3; "bab" is returned here.

## Example 2

### Input

```text
s = "cbbd"
```

### Expected Output

```text
"bb"
```

### Explanation

"bb" is the longest palindromic substring.

## How to Solve

### Key Idea

Every palindrome has a center (a single character for odd-length ones, or a gap between two characters for even-length ones). If you expand outward from each possible center as long as the characters match, you'll find every palindrome in the string.

### Approach

1. Loop over every index in the string, treating it as a potential center.
2. For each index, expand outward twice: once assuming an odd-length palindrome (center = single character), once assuming an even-length palindrome (center = gap between two characters).
3. Each expansion grows left and right pointers outward while the characters match, tracking the longest match found.
4. Keep track of the best (longest) palindrome seen across all centers and return it.

### Why It Works

Any palindrome is symmetric around some center point. By trying all `2n - 1` possible centers (n single-character centers plus n-1 gaps) and expanding outward, you're guaranteed to discover every palindromic substring, including the longest one.

## Visual Explanation

```text
s = "babad"   indices: b0 a1 b2 a3 d4

i=1: expand(1,1) -> s[0]==s[2] ('b'=='b'), then s[-1] out of range, stop -> "bab" (len 3)
i=2: expand(2,2) -> s[1]==s[3] ('a'=='a'), then s[0]!='d', stop      -> "aba" (len 3, found later)

Both are length 3, but "bab" is found first (at i=1), so it's the one returned.
```

## Optimal Solution

```python
class Solution:
    def longestPalindrome(self, s: str) -> str:
        if not s:
            return ""

        start, end = 0, 0

        def expand(left: int, right: int) -> tuple[int, int]:
            while left >= 0 and right < len(s) and s[left] == s[right]:
                left -= 1
                right += 1
            return left + 1, right - 1

        for i in range(len(s)):
            l1, r1 = expand(i, i)
            if r1 - l1 > end - start:
                start, end = l1, r1

            l2, r2 = expand(i, i + 1)
            if r2 - l2 > end - start:
                start, end = l2, r2

        return s[start:end + 1]
```

## Complexity

- **Time:** O(n^2)
- **Space:** O(1)

## Real-World Uses

- Detecting palindromic patterns in DNA/RNA sequences for bioinformatics analysis.
- Finding symmetric substrings in text processing for data validation or pattern matching.
- Identifying palindromic identifiers or codes in data cleansing pipelines.

## Key Takeaway

"Expand around center" is a simple and efficient way to find palindromes without building a full DP table, and it works because every palindrome has exactly one center of symmetry.
