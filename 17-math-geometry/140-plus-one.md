# 140. Plus One

## Problem

You are given a large integer represented as an array of digits, where each element is a single digit and the digits are ordered from most significant to least significant (no leading zeros, except the number 0 itself). Add one to the number and return the resulting array of digits.

## Example 1

### Input

```text
digits = [1,2,3]
```

### Expected Output

```text
[1,2,4]
```

### Explanation

The number 123 plus one is 124.

## Example 2

### Input

```text
digits = [9,9,9]
```

### Expected Output

```text
[1,0,0,0]
```

### Explanation

999 plus one is 1000, which needs an extra digit at the front.

## How to Solve

### Key Idea

Adding one only affects trailing 9s through a carry. Walk from the last digit backward, and only when every digit is 9 do you need to grow the array by one extra digit at the front.

### Approach

1. Iterate over the digits array from the last index to the first.
2. If the current digit is less than 9, increment it by 1 and return the array immediately (no more carrying needed).
3. If the current digit is 9, set it to 0 (carry the 1 to the next position) and continue to the previous digit.
4. If the loop finishes (meaning every digit was 9), prepend a 1 to the front of the array.

### Why It Works

Adding one to a number only ripples past a digit if that digit was 9 (turning into 0 with a carry). As soon as we hit a digit that isn't 9, incrementing it absorbs the carry and nothing further changes. Only an all-9s number (like 999) needs a new leading digit.

## Visual Explanation

```text
[9, 9, 9]
   |  |  |
   0  0  0  + carry -> prepend 1
[1, 0, 0, 0]
```

## Optimal Solution

```python
class Solution:
    def plusOne(self, digits: list[int]) -> list[int]:
        n = len(digits)
        for i in range(n - 1, -1, -1):
            if digits[i] < 9:
                digits[i] += 1
                return digits
            digits[i] = 0

        return [1] + digits
```

## Complexity

- **Time:** O(n), where n is the number of digits
- **Space:** O(1) extra space, ignoring the output array (O(n) only in the rare all-9s case)

## Real-World Uses

- Arbitrary-precision counters (odometers, view counts) that exceed native integer digit arrays.
- Big-number libraries incrementing values stored as digit arrays.
- Version-number or ID incrementing logic that must handle digit rollover cleanly.

## Key Takeaway

Simulating manual addition digit-by-digit with carry propagation handles arbitrarily large numbers that don't fit in normal integer types, and is a pattern reused in string/array addition problems.
