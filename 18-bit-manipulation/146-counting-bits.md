# 146. Counting Bits

## Problem

Given an integer n, return an array of length n + 1 where each index i holds the number of 1 bits in the binary representation of i. For example, index 5 should hold the count of set bits in the number 5.

## Example 1

### Input

```text
n = 2
```

### Expected Output

```text
[0, 1, 1]
```

### Explanation

0 has 0 set bits, 1 has 1 set bit, 2 (binary 10) has 1 set bit.

## Example 2

### Input

```text
n = 5
```

### Expected Output

```text
[0, 1, 1, 2, 1, 2]
```

### Explanation

0→0, 1→1, 2→1, 3(11)→2, 4(100)→1, 5(101)→2.

## How to Solve

### Key Idea

Use dynamic programming: the number of set bits in i can be derived from a smaller number you've already computed, by looking at i with its lowest set bit removed, or by looking at i shifted right by one.

### Approach

1. Create a result array of size n + 1, with result[0] = 0.
2. For each i from 1 to n, compute result[i] = result[i >> 1] + (i & 1).
3. This says: the bit count of i equals the bit count of i with the last bit dropped, plus 1 if that last bit was a 1.
4. Return the result array.

### Why It Works

Shifting i right by one bit removes the last bit, and that smaller number's bit count was already computed earlier in the loop. Adding back whether the removed bit was 1 or 0 gives the exact count for i, so each answer builds on previous answers in O(1) extra work.

## Visual Explanation

```text
i = 5 -> binary 101
i >> 1 = 10 (2), i & 1 = 1
result[5] = result[2] + 1 = 1 + 1 = 2
```

## Optimal Solution

```python
from typing import List

class Solution:
    def countBits(self, n: int) -> List[int]:
        result = [0] * (n + 1)
        for i in range(1, n + 1):
            result[i] = result[i >> 1] + (i & 1)
        return result
```

## Complexity

- **Time:** O(n)
- **Space:** O(n) for the output array (O(1) extra space besides it)

## Real-World Uses

- Precomputing bit-count tables for fast lookups in embedded systems with limited CPU cycles.
- Analyzing bitmask distributions in network packet flags or compression formats.
- Building frequency tables for hashing schemes that rely on bit patterns.

## Key Takeaway

When a problem asks for a value at every index up to n, look for a way to build each answer from a previously computed smaller answer — this turns an O(n log n) brute force into O(n).
