# 105. Decode Ways

## Problem

A message containing letters A-Z is encoded using the mapping `'A' -> "1"`, `'B' -> "2"`, ..., `'Z' -> "26"`. Given a string of digits, count how many ways it can be decoded. The string may contain zeros, which can only be valid as part of a two-digit code (10-26).

## Example 1

### Input

```text
s = "12"
```

### Expected Output

```text
2
```

### Explanation

"12" can be decoded as "AB" (1, 2) or "L" (12).

## Example 2

### Input

```text
s = "226"
```

### Expected Output

```text
3
```

### Explanation

"226" can be decoded as "BZ" (2, 26), "VF" (22, 6), or "BBF" (2, 2, 6).

## How to Solve

### Key Idea

The number of ways to decode up to position `i` depends on whether the last one digit forms a valid letter (1-9) and whether the last two digits form a valid letter (10-26). Add up the ways from both valid options.

### Approach

1. Track the number of ways to decode the string up to the previous position and the position before that.
2. Walk through the string from left to right.
3. If the current single digit (1-9) is valid, add the "one position back" count.
4. If the last two digits (10-26) are valid, add the "two positions back" count.
5. If neither is valid, there are zero ways from this point, which naturally propagates forward.

### Why It Works

Every decoding of the prefix ending at position `i` must end with either a one-digit code or a two-digit code. Counting the valid ways to end each way and summing them covers every possible decoding without double-counting.

## Visual Explanation

```text
s = "12"
dp[0] = 1   (empty prefix)
dp[1] = 1   ("1" -> "A")
dp[2] = dp[1] + dp[0] = 1 + 1 = 2   ("2" is valid alone, "12" is valid as one code)
```

## Optimal Solution

```python
class Solution:
    def numDecodings(self, s: str) -> int:
        if not s or s[0] == '0':
            return 0

        # two_back = ways to decode s[:i-1], one_back = ways to decode s[:i]
        two_back, one_back = 1, 1

        for i in range(1, len(s)):
            current = 0
            one_digit = s[i]
            two_digit = s[i - 1:i + 1]

            if one_digit != '0':
                current += one_back
            if '10' <= two_digit <= '26':
                current += two_back

            two_back, one_back = one_back, current

        return one_back
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Counting valid decodings in custom message/protocol encoding schemes.
- Parsing ambiguous numeric input streams (e.g., legacy data formats) into valid tokens.
- Validating checksum or encoding schemes in telecom/data transmission systems.

## Key Takeaway

Whenever a step can be taken in one of a few fixed-size chunks (here, 1 or 2 digits), the DP recurrence just sums the valid chunk options from earlier states — the same shape as Climbing Stairs, with validity checks added.
