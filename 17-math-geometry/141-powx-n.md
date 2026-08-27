# 141. Pow(x, n)

## Problem

Implement a function that calculates `x` raised to the power `n` (that is, `x^n`), where `x` is a float and `n` is an integer that can be positive, negative, or zero. Negative exponents mean `x^n = 1 / x^(-n)`.

## Example 1

### Input

```text
x = 2.00000, n = 10
```

### Expected Output

```text
1024.00000
```

### Explanation

2 raised to the 10th power is 1024.

## Example 2

### Input

```text
x = 2.00000, n = -2
```

### Expected Output

```text
0.25000
```

### Explanation

2^-2 = 1 / 2^2 = 1 / 4 = 0.25.

## How to Solve

### Key Idea

Instead of multiplying `x` by itself `n` times (which is O(n)), use "fast exponentiation": repeatedly square the base and halve the exponent, only multiplying into the result when the exponent bit is set.

### Approach

1. If `n` is negative, convert the problem to `1 / x^(-n)` and work with the positive exponent.
2. Initialize `result = 1`.
3. While the exponent is greater than 0: if it's odd, multiply `result` by the current base; then square the base and halve the exponent (integer division).
4. Return the final result (inverted if the original `n` was negative).

### Why It Works

Any exponent can be written in binary, and `x^n` can be built by squaring `x` repeatedly (`x, x^2, x^4, x^8, ...`) and multiplying together only the powers corresponding to 1-bits in `n`'s binary representation. This cuts the number of multiplications from O(n) down to O(log n).

## Visual Explanation

```text
exponent=10 (even): base=x     -> no multiply;      square base to x^2,  exponent -> 5
exponent=5  (odd):  base=x^2   -> result *= x^2;    square base to x^4,  exponent -> 2
exponent=2  (even): base=x^4   -> no multiply;      square base to x^8,  exponent -> 1
exponent=1  (odd):  base=x^8   -> result *= x^8;    square base to x^16, exponent -> 0

result = x^2 * x^8 = x^10 = 2^10 = 1024
```

## Optimal Solution

```python
class Solution:
    def myPow(self, x: float, n: int) -> float:
        if n < 0:
            x = 1 / x
            n = -n

        result = 1.0
        base = x
        exponent = n

        while exponent > 0:
            if exponent % 2 == 1:
                result *= base
            base *= base
            exponent //= 2

        return result
```

## Complexity

- **Time:** O(log n)
- **Space:** O(1) extra space (iterative version)

## Real-World Uses

- Fast modular exponentiation used in RSA and other cryptographic algorithms.
- Efficient computation of compound interest or growth formulas over large exponents.
- Matrix exponentiation used to compute Fibonacci numbers or graph reachability quickly.

## Key Takeaway

Fast exponentiation (repeated squaring) turns an O(n) power computation into O(log n), and the same "square and halve" idea shows up in matrix exponentiation and modular exponentiation problems.
