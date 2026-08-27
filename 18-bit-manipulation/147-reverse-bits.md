# 147. Reverse Bits

## Problem

Given a 32-bit unsigned integer, reverse the order of its bits and return the resulting 32-bit unsigned integer. The first bit becomes the last, the second becomes the second-to-last, and so on.

## Example 1

### Input

```text
n = 00000010100101000001111010011100
```

### Expected Output

```text
00111001011110000010100101000000
```

### Explanation

Reading the input bits from right to left gives the output bits from left to right.

## Example 2

### Input

```text
n = 11111111111111111111111111111101
```

### Expected Output

```text
10111111111111111111111111111111
```

### Explanation

The bits are reversed end to end; only the two rightmost bits of the input (0 and 1) differ from being all 1s, so they end up at the leftmost positions of the output, swapped in order.

## How to Solve

### Key Idea

Build the answer one bit at a time: take the lowest bit off the input and place it into the result from the highest position down, shifting both numbers as you go.

### Approach

1. Initialize result to 0.
2. Loop 32 times (once per bit).
3. Each iteration: shift result left by 1, then OR in the lowest bit of n (n & 1), then shift n right by 1.
4. After 32 iterations, result holds the bit-reversed value.

### Why It Works

Shifting result left by 1 each time and appending the next lowest bit of n effectively writes n's bits in reverse order, since the first bit you read (n's lowest bit) ends up pushed to the highest position by the end of the loop.

## Visual Explanation

Using a small 4-bit example to see the mirroring clearly (`1011` becomes `1101`):

```text
Input:  1011  (read bits from the right/lowest end first: 1, 1, 0, 1)

result starts at 0
step 1: take bit 1 (lowest of input) -> result = 0001
step 2: take bit 1 (next)           -> result = 0011
step 3: take bit 0 (next)           -> result = 0110
step 4: take bit 1 (highest)        -> result = 1101

Output: 1101
```

Each step peels the lowest bit off the input and appends it to the *end* of the result, which is exactly what reverses the bit order.

## Optimal Solution

```python
class Solution:
    def reverseBits(self, n: int) -> int:
        result = 0
        for _ in range(32):
            result = (result << 1) | (n & 1)
            n >>= 1
        return result
```

## Complexity

- **Time:** O(1) (always exactly 32 iterations)
- **Space:** O(1)

## Real-World Uses

- Endianness conversion when transmitting data between systems with different byte/bit orders.
- Bit-reversal permutation used as a preprocessing step in the Fast Fourier Transform (FFT).
- Low-level protocol and hardware register manipulation in embedded systems.

## Key Takeaway

Reversing bits is a fixed 32-step build-up process: peel a bit off one end, append it to the other. This shift-and-OR pattern shows up often in bit manipulation problems.
