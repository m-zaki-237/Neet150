# 149. Sum of Two Integers

## Problem

Given two integers a and b, return their sum without using the `+` or `-` operators. You must add them using only bitwise operations. The result should behave like a signed 32-bit integer.

## Example 1

### Input

```text
a = 1, b = 2
```

### Expected Output

```text
3
```

### Explanation

Adding 1 and 2 with bitwise logic still gives 3.

## Example 2

### Input

```text
a = 2, b = 3
```

### Expected Output

```text
5
```

### Explanation

Adding 2 and 3 with bitwise logic gives 5.

## How to Solve

### Key Idea

Addition without `+` can be done bit by bit using XOR and AND: XOR gives you the sum of each bit ignoring carries, and AND (shifted left by 1) gives you the carries. Keep applying this until there are no carries left.

### Approach

1. Repeat while there is a nonzero carry: compute `carry = (a & b) << 1` and `a = a ^ b`, then set `b = carry`.
2. Because Python integers don't have a fixed width, mask every intermediate value with `0xFFFFFFFF` to simulate 32-bit behavior.
3. Once the loop ends, if the result looks negative in 32-bit two's complement (its top bit is set), convert it back to a proper negative Python integer.
4. Return the final value.

### Why It Works

XOR adds two bits without carrying (1+1 wraps to 0), and AND followed by a left shift produces exactly the carry that addition would generate at each position. Repeating this until the carry becomes 0 reproduces normal binary addition. Masking to 32 bits and converting the top bit back to a sign matches fixed-width integer behavior, since Python ints don't overflow on their own.

## Visual Explanation

```text
a = 011 (3)
b = 001 (1)

XOR (sum, no carry): 011 ^ 001 = 010
AND<<1 (carry):     (011 & 001) << 1 = 010

Repeat: 010 ^ 010 = 000, carry (010 & 010) << 1 = 100
Repeat: 000 ^ 100 = 100, carry 0 -> stop
Result: 100 = 4
```

## Optimal Solution

```python
class Solution:
    def getSum(self, a: int, b: int) -> int:
        mask = 0xFFFFFFFF
        while b & mask:
            carry = (a & b) << 1
            a = (a ^ b) & mask
            b = carry & mask
        result = a & mask
        # If the sign bit (bit 31) is set, convert from unsigned to signed 32-bit
        if result > 0x7FFFFFFF:
            result = ~(result ^ mask)
        return result
```

## Complexity

- **Time:** O(1) (bounded by 32 bit positions)
- **Space:** O(1)

## Real-World Uses

- Implementing arithmetic logic units (ALUs) in digital circuit and processor design.
- Performing addition in cryptographic systems that must avoid native overflow-prone operators.
- Fixed-width arithmetic emulation in embedded systems lacking native large-integer support.

## Key Takeaway

XOR simulates carry-less addition and AND-then-shift simulates carry generation — combining them in a loop reproduces full binary addition using only bitwise operators, and masking handles fixed-width overflow in languages like Python that don't have it natively.
