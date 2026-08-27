# 148. Missing Number

## Problem

You are given an array containing n distinct numbers taken from the range 0 to n (inclusive), so exactly one number in that range is missing from the array. Find and return the missing number.

## Example 1

### Input

```text
nums = [3, 0, 1]
```

### Expected Output

```text
2
```

### Explanation

n = 3, so the full range is 0 to 3. The array contains 3, 0, 1 — missing 2.

## Example 2

### Input

```text
nums = [0, 1]
```

### Expected Output

```text
2
```

### Explanation

n = 2, so the full range is 0 to 2. The array contains 0, 1 — missing 2.

## How to Solve

### Key Idea

XOR every index and every value in the array together with the numbers 0 through n. Since XOR cancels out matching pairs, everything that appears in both the "expected" range and the array cancels, leaving only the missing number.

### Approach

1. Let n be the length of the array (since one number in 0..n is missing).
2. Start a result variable at n.
3. For each index i from 0 to n - 1, XOR result with i and with nums[i].
4. Return result — the number that never got cancelled out.

### Why It Works

XOR-ing 0 through n against every value actually present cancels every number that's in the array, since each present number appears once as an "expected" value and once as an actual value. The one number missing from the array only appears once (as an expected value), so it survives the cancellation.

## Visual Explanation

```text
n = 3, nums = [3, 0, 1]
result = 3
^ 0 ^ 3  -> 0
^ 1 ^ 0  -> 1
^ 2 ^ 1  -> 2
Final result = 2 (the missing number)
```

## Optimal Solution

```python
from typing import List

class Solution:
    def missingNumber(self, nums: List[int]) -> int:
        n = len(nums)
        result = n
        for i in range(n):
            result ^= i ^ nums[i]
        return result
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Detecting a missing sequence ID in network packet streams without extra storage.
- Verifying data integrity by cross-checking expected vs. received record identifiers.
- Lightweight duplicate/missing-element detection in embedded systems with tight memory budgets.

## Key Takeaway

XOR-ing a full expected range against the actual values is a neat constant-space way to find a single missing or duplicate element, avoiding sorting or extra hash sets.
