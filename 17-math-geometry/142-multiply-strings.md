# 142. Multiply Strings

## Problem

Given two non-negative integers `num1` and `num2` represented as strings, return the product of `num1` and `num2`, also as a string. You must not convert the inputs directly into built-in integer types (like Python's `int`) and multiply them — you need to simulate the multiplication manually, digit by digit.

## Example 1

### Input

```text
num1 = "2", num2 = "3"
```

### Expected Output

```text
"6"
```

### Explanation

2 * 3 = 6.

## Example 2

### Input

```text
num1 = "123", num2 = "456"
```

### Expected Output

```text
"56088"
```

### Explanation

123 * 456 = 56088, computed via digit-by-digit multiplication and carrying, just like doing it by hand.

## How to Solve

### Key Idea

Multiply the numbers the way you would by hand on paper: multiply every digit of `num1` by every digit of `num2`, and place each partial product at the correct position in a result array, handling carries at the end.

### Approach

1. If either input is `"0"`, return `"0"` immediately.
2. Create a result array of size `len(num1) + len(num2)` filled with zeros (the product can never need more digits than that).
3. For each digit `i` in `num1` (from the last index) and each digit `j` in `num2` (from the last index), multiply the two digits and add the product to `result[i + j + 1]`, then propagate any carry into `result[i + j]`.
4. After processing all digit pairs, convert the result array into a string, skipping any leading zeros.

### Why It Works

Multiplying digit `i` of `num1` (from the right, 0-indexed) by digit `j` of `num2` contributes to position `i + j` (and `i + j + 1` for the ones-place of that partial product) in the final result, mirroring how place values combine in long multiplication. Accumulating all partial products into the correct positions and resolving carries afterward reproduces the exact result of full multiplication.

## Visual Explanation

```text
num1 = "123", num2 = "456"   (result array has size 3+3 = 6, indices 0..5)

i=2 (digit 3) * j=2 (digit 6) = 18
  -> result[i+j+1] = result[5] += 18   (units digit 8, carry 1 into result[4])

i=2 (digit 3) * j=1 (digit 5) = 15
  -> result[i+j+1] = result[4] += 15 + carried 1

... after all 9 digit pairs are accumulated this way and carries resolved ...

result array = [0, 5, 6, 0, 8, 8] -> drop leading zero -> "56088"
```

## Optimal Solution

```python
class Solution:
    def multiply(self, num1: str, num2: str) -> str:
        if num1 == "0" or num2 == "0":
            return "0"

        n1, n2 = len(num1), len(num2)
        result = [0] * (n1 + n2)

        for i in range(n1 - 1, -1, -1):
            digit1 = ord(num1[i]) - ord('0')
            for j in range(n2 - 1, -1, -1):
                digit2 = ord(num2[j]) - ord('0')
                product = digit1 * digit2
                pos_low = i + j + 1
                pos_high = i + j

                total = product + result[pos_low]
                result[pos_low] = total % 10
                result[pos_high] += total // 10

        # Skip leading zeros
        start = 0
        while start < len(result) - 1 and result[start] == 0:
            start += 1

        return ''.join(map(str, result[start:]))
```

## Complexity

- **Time:** O(n * m), where n and m are the lengths of num1 and num2
- **Space:** O(n + m) for the result array

## Real-World Uses

- Arbitrary-precision arithmetic libraries (bignum libraries) multiplying numbers larger than native types support.
- Cryptographic algorithms that multiply very large integers exactly.
- Financial and scientific computing systems requiring exact decimal/integer precision beyond floating point.

## Key Takeaway

Simulating grade-school long multiplication digit by digit, with a result array indexed by combined place value, is the standard way to multiply arbitrarily large numbers without relying on built-in big-integer support.
