# 110. Partition Equal Subset Sum

## Problem

Given an array of positive integers `nums`, determine if you can split it into two subsets such that the sums of both subsets are equal.

## Example 1

### Input

```text
nums = [1, 5, 11, 5]
```

### Expected Output

```text
true
```

### Explanation

The array can be split into [1, 5, 5] and [11], both summing to 11.

## Example 2

### Input

```text
nums = [1, 2, 3, 5]
```

### Expected Output

```text
false
```

### Explanation

The total sum is 11, which is odd, so it can't be split into two equal halves.

## How to Solve

### Key Idea

If the total sum is odd, it's impossible to split evenly, so return false right away. Otherwise, the problem becomes "can some subset of the numbers add up to exactly half the total sum?" which is the classic 0/1 knapsack "subset sum" problem.

### Approach

1. Compute the total sum. If it's odd, return false immediately.
2. Set the target as half the total sum.
3. Use a boolean set (or array) `dp` where `dp[s]` means "sum `s` is achievable using numbers processed so far." Start with only `dp[0] = True`.
4. For each number, update the achievable sums by adding that number to sums already known to be achievable, processing sums from high to low to avoid reusing the same number twice.
5. Return whether the target sum is achievable.

### Why It Works

Each number can either be included in the target-sum subset or not, which is a classic 0/1 knapsack choice. Processing achievable sums from largest to smallest ensures each number is only used once per subset, since it doesn't get combined with a sum that was already updated using the same number in this same pass.

## Visual Explanation

```text
dp[s] = dp[s] OR dp[s - num]   (processed s from target down to num)
target = total_sum / 2
```

## Optimal Solution

```python
class Solution:
    def canPartition(self, nums: list[int]) -> bool:
        total = sum(nums)
        if total % 2 != 0:
            return False

        target = total // 2
        dp = [False] * (target + 1)
        dp[0] = True

        for num in nums:
            for s in range(target, num - 1, -1):
                if dp[s - num]:
                    dp[s] = True

        return dp[target]
```

## Complexity

- **Time:** O(n * target), where target is half the total sum
- **Space:** O(target)

## Real-World Uses

- Balancing workload or inventory between two teams/warehouses so totals match.
- Splitting a set of tasks or costs evenly between two budgets or invoices.
- Load-balancing jobs across two machines to equalize total processing time.

## Key Takeaway

Partitioning into equal halves reduces to the classic 0/1 knapsack "subset sum" pattern — iterate sums backward per item so each item is only used once.
