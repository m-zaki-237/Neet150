# 150. Reverse Integer

## Problem

Given a signed 32-bit integer, reverse its digits. If reversing causes the value to go outside the signed 32-bit integer range (-2147483648 to 2147483647), return 0 instead.

## Example 1

### Input

```text
x = 123
```

### Expected Output

```text
321
```

### Explanation

Reversing the digits of 123 gives 321, which fits in the 32-bit signed range.

## Example 2

### Input

```text
x = 1534236469
```

### Expected Output

```text
0
```

### Explanation

Reversing 1534236469 gives 9646324351, which is larger than 2147483647, so the function returns 0.

## How to Solve

### Key Idea

Treat the number as a string of digits (handling the sign separately) or peel off digits one at a time using modulo and integer division, then check the final result against the signed 32-bit bounds.

### Approach

1. Record whether the number is negative, and work with its absolute value.
2. Repeatedly take the last digit (`num % 10`) and append it to a growing reversed number, then remove that digit from num (`num //= 10`).
3. Reapply the original sign to the reversed number.
4. If the reversed number falls outside -2147483648 to 2147483647, return 0; otherwise return it.

### Why It Works

Peeling digits from the end and building a new number in that order naturally reverses the digit sequence. Checking the final value against the 32-bit signed range directly enforces the overflow rule the problem asks for.

## Visual Explanation

```text
x = 123
digit=3, x=12,  reversed = 0*10+3 = 3
digit=2, x=1,   reversed = 3*10+2 = 32
digit=1, x=0,   reversed = 32*10+1 = 321
Result: 321
```

## Optimal Solution

```python
class Solution:
    def reverse(self, x: int) -> int:
        INT_MIN, INT_MAX = -2**31, 2**31 - 1

        sign = -1 if x < 0 else 1
        x = abs(x)

        reversed_num = 0
        while x != 0:
            digit = x % 10
            x //= 10
            reversed_num = reversed_num * 10 + digit

        reversed_num *= sign

        if reversed_num < INT_MIN or reversed_num > INT_MAX:
            return 0
        return reversed_num
```

## Complexity

- **Time:** O(log10(x)), proportional to the number of digits
- **Space:** O(1)

## Real-World Uses

- Validating fixed-width integer overflow behavior in low-level systems and embedded firmware.
- Digit manipulation checks used in checksum or ID-validation routines.
- Palindrome/number-formatting utilities in data validation and serialization layers.

## Key Takeaway

Digit-reversal problems usually come down to repeatedly extracting the last digit with modulo and division — the tricky part is remembering to check the result against the required bounds afterward.
