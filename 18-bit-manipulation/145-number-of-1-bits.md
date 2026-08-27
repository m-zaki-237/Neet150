# 145. Number of 1 Bits

## Problem

Given an unsigned integer, count how many bits in its binary representation are set to 1. This is often called the "Hamming weight" of the number.

## Example 1

### Input

```text
n = 11 (binary: 00000000000000000000000000001011)
```

### Expected Output

```text
3
```

### Explanation

The binary form has three 1 bits (positions for 8, 2, and 1).

## Example 2

### Input

```text
n = 128 (binary: 00000000000000000000000010000000)
```

### Expected Output

```text
1
```

### Explanation

128 is a power of two, so it has exactly one 1 bit.

## How to Solve

### Key Idea

Repeatedly strip off the lowest set bit using the trick `n & (n - 1)`, which turns the rightmost 1 bit into a 0. Count how many times you can do this before the number becomes 0.

### Approach

1. Initialize a counter to 0.
2. While n is not 0, do `n = n & (n - 1)` and increment the counter.
3. Return the counter once n becomes 0.

### Why It Works

Subtracting 1 from a number flips all the trailing zeros to ones and the lowest set bit to zero. ANDing with the original number keeps everything the same except that lowest set bit, which becomes 0. So each iteration removes exactly one 1 bit.

## Visual Explanation

```text
n = 11 -> 1011

Step 1: n=1011, n-1=1010, n&(n-1) = 1011 & 1010 = 1010  (count=1)
Step 2: n=1010, n-1=1001, n&(n-1) = 1010 & 1001 = 1000  (count=2)
Step 3: n=1000, n-1=0111, n&(n-1) = 1000 & 0111 = 0000  (count=3)

n is now 0 -> stop, count = 3
```

## Optimal Solution

```python
class Solution:
    def hammingWeight(self, n: int) -> int:
        count = 0
        while n:
            n &= (n - 1)  # Brian Kernighan's algorithm: clears the lowest set bit
            count += 1
        return count
```

## Complexity

- **Time:** O(k), where k is the number of set bits (at most 32)
- **Space:** O(1)

## Real-World Uses

- Computing Hamming weight/distance in error-correcting codes and network transmission checks.
- Counting active permission or role bits in a bitmask-based access-control system.
- Population-count instructions used in cryptographic algorithms and hashing functions.

## Key Takeaway

`n & (n - 1)` is a classic trick to clear the lowest set bit — it's faster than checking every single bit position when the number of 1 bits is small.
