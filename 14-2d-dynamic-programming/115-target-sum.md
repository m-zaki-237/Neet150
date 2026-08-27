# 115. Target Sum

## Problem

You are given an array of non-negative integers `nums` and an integer `target`. For each number you must choose a `+` or `-` sign and add them all up. Count the number of ways to assign signs so the total equals `target`.

## Example 1

### Input

```text
nums = [1,1,1,1,1], target = 3
```

### Expected Output

```text
5
```

### Explanation

There are 5 ways to place +/- signs among the five 1s to reach a sum of 3, e.g. +1+1+1+1-1, +1+1+1-1+1, and so on.

## Example 2

### Input

```text
nums = [1], target = 1
```

### Expected Output

```text
1
```

### Explanation

Only "+1" reaches the target of 1.

## How to Solve

### Key Idea

Track, for every possible running sum, how many ways exist to reach it after considering some prefix of the numbers — this is a 2D DP over (index, current sum) collapsed into a dictionary/array of sums.

### Approach

1. Use a dictionary `dp` mapping a running sum to the number of ways to reach it, starting with `{0: 1}`.
2. For each number in `nums`, build a new dictionary where each existing sum `s` contributes to `s + num` and `s - num`.
3. Replace `dp` with the new dictionary after processing each number.
4. Return `dp.get(target, 0)` at the end.

### Why It Works

Every way to reach a sum after `i` numbers extends a way to reach some sum after `i-1` numbers, either by adding or subtracting the next number, so tracking counts per sum at each step correctly accumulates every sign combination.

## Visual Explanation

```text
nums = [1,1,1,1,1], target = 3
Running {sum: ways} after processing each 1:

after 1st: { 1:1, -1:1 }
after 2nd: { 2:1, 0:2, -2:1 }
after 3rd: { 3:1, 1:3, -1:3, -3:1 }
after 4th: { 4:1, 2:4, 0:6, -2:4, -4:1 }
after 5th: { 5:1, 3:5, 1:10, -1:10, -3:5, -5:1 }

dp[target] = dp[3] = 5  <- matches Example 1's output
```

## Optimal Solution

```python
from typing import List
from collections import defaultdict

class Solution:
    def findTargetSumWays(self, nums: List[int], target: int) -> int:
        dp = defaultdict(int)
        dp[0] = 1

        for num in nums:
            next_dp = defaultdict(int)
            for total, count in dp.items():
                next_dp[total + num] += count
                next_dp[total - num] += count
            dp = next_dp

        return dp[target]
```

## Complexity

- **Time:** O(n * S) where S is the range of possible sums
- **Space:** O(S)

## Real-World Uses

- Counting valid configurations in constraint-satisfaction puzzles (assigning +/- or on/off states).
- Portfolio scenario analysis where each asset's contribution can be positive or negative.
- Combinatorial voting/allocation systems counting outcomes that hit an exact target.

## Key Takeaway

Problems about counting sign/choice assignments to hit a target sum can be modeled as a 2D DP of (item index, running sum), often implemented efficiently with a hash map instead of a full array.
