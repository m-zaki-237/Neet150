# 144. Single Number

## Problem

You are given an array of integers where every number appears exactly twice, except for one number that appears only once. Find that single number. Your solution should ideally run in linear time and use constant extra space (no hash maps).

## Example 1

### Input

```text
nums = [2, 2, 1]
```

### Expected Output

```text
1
```

### Explanation

2 appears twice and 1 appears once, so 1 is the answer.

## Example 2

### Input

```text
nums = [4, 1, 2, 1, 2]
```

### Expected Output

```text
4
```

### Explanation

1 and 2 each appear twice. 4 is the only number that appears once.

## How to Solve

### Key Idea

XOR-ing a number with itself gives 0, and XOR-ing with 0 leaves a number unchanged. So if you XOR every number in the array together, all the pairs cancel out and only the single number remains.

### Approach

1. Start a running result variable at 0.
2. XOR the result with every number in the array, one at a time.
3. Return the final result — it's the number that had no pair.

### Why It Works

XOR is commutative and associative, so order doesn't matter. Every duplicate pair XORs to 0 and disappears, leaving only the unpaired value.

## Visual Explanation

```text
2 = 010
2 = 010
1 = 001
XOR all: 010 ^ 010 ^ 001 = 001 = 1
```

## Optimal Solution

```python
from typing import List

class Solution:
    def singleNumber(self, nums: List[int]) -> int:
        result = 0
        for num in nums:
            result ^= num
        return result
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Toggling feature flags or permission bits where applying the same flag twice should cancel out.
- Parity/checksum bits in network protocols, where XOR detects single differing bits.
- Simple symmetric encryption (XOR ciphers) where encrypting twice with the same key restores the original data.

## Key Takeaway

XOR is a powerful trick for "find the odd one out" problems where everything else appears in pairs — it lets you avoid extra memory entirely.
