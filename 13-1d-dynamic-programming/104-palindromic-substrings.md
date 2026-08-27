# 104. Palindromic Substrings

## Problem

Given a string `s`, count how many substrings of it are palindromes (read the same forward and backward). Substrings starting at different indices or ending at different indices are counted separately, even if they contain the same characters.

## Example 1

### Input

```text
s = "abc"
```

### Expected Output

```text
3
```

### Explanation

The palindromic substrings are "a", "b", "c" — 3 total.

## Example 2

### Input

```text
s = "aaa"
```

### Expected Output

```text
6
```

### Explanation

The palindromic substrings are "a", "a", "a", "aa", "aa", "aaa" — 6 total.

## How to Solve

### Key Idea

Just like Longest Palindromic Substring, expand outward from every possible center. Every time the expansion finds a valid palindrome, count it.

### Approach

1. Loop over every index in the string as a potential center.
2. For each index, expand outward assuming an odd-length palindrome centered there, counting one valid palindrome each time the characters still match.
3. Do the same expansion assuming an even-length palindrome centered between that index and the next.
4. Sum up all the counts from every center.

### Why It Works

Each expansion step that finds matching characters corresponds to exactly one distinct palindromic substring. Trying all `2n - 1` centers guarantees every palindromic substring gets counted exactly once.

## Visual Explanation

```text
s = "aaa"
i=0: expand(0,0)=1, expand(0,1)=1   -> +2
i=1: expand(1,1)=2, expand(1,2)=1   -> +3
i=2: expand(2,2)=1, expand(2,3)=0   -> +1
total = 2 + 3 + 1 = 6   <- matches expected output
```

## Optimal Solution

```python
class Solution:
    def countSubstrings(self, s: str) -> int:
        count = 0

        def expand(left: int, right: int) -> int:
            local_count = 0
            while left >= 0 and right < len(s) and s[left] == s[right]:
                local_count += 1
                left -= 1
                right += 1
            return local_count

        for i in range(len(s)):
            count += expand(i, i)
            count += expand(i, i + 1)

        return count
```

## Complexity

- **Time:** O(n^2)
- **Space:** O(1)

## Real-World Uses

- Counting symmetric patterns in genomic sequence analysis tools.
- Auditing text data for repeated/mirrored substrings in plagiarism or duplicate detection.
- Feature extraction for string-similarity scoring in search and NLP pipelines.

## Key Takeaway

Counting palindromes and finding the longest one use the same "expand around center" technique — once you know the pattern, you can adapt it to count instead of just track the maximum.
