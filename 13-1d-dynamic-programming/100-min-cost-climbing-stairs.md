# 100. Min Cost Climbing Stairs

## Problem

You are given an array `cost` where `cost[i]` is the cost of stepping on stair `i`. You can start from step 0 or step 1, and from a step you can move 1 or 2 steps forward. Find the minimum total cost to reach just past the last stair (the "top").

## Example 1

### Input

```text
cost = [10, 15, 20]
```

### Expected Output

```text
15
```

### Explanation

Start at index 1 (cost 15), then step 2 to reach the top. Total cost = 15.

## Example 2

### Input

```text
cost = [1, 100, 1, 1, 1, 100, 1, 1, 100, 1]
```

### Expected Output

```text
6
```

### Explanation

Take indices 0,2,4,6,7,9 (skipping the expensive 100s), total cost = 6.

## How to Solve

### Key Idea

The cheapest way to reach the top from a given step is the cost of that step plus the cheapest of reaching it from one or two steps back. Build this up from the bottom.

### Approach

1. Keep two variables for the minimum cost to reach the two most recent steps.
2. For each step, compute its cost as `cost[i] + min(previous two costs)`.
3. Slide the two variables forward through the array.
4. The answer is the minimum of the last two computed values, since you can jump into the "top" from either of the last two steps.

### Why It Works

Since you can only move 1 or 2 steps, the cheapest way to arrive anywhere depends only on the cheapest way to arrive at the two steps right before it. This is a classic optimal-substructure DP.

## Visual Explanation

```text
cost = [10, 15, 20]
dp[0] = 0, dp[1] = 0                                  (free start on either step)
dp[2] = min(dp[1] + cost[1], dp[0] + cost[0]) = min(15, 10) = 10
dp[3] = min(dp[2] + cost[2], dp[1] + cost[1]) = min(30, 15) = 15   <- matches expected output
```

## Optimal Solution

```python
class Solution:
    def minCostClimbingStairs(self, cost: list[int]) -> int:
        two_back, one_back = 0, 0
        for i in range(2, len(cost) + 1):
            current = min(one_back + cost[i - 1], two_back + cost[i - 2])
            two_back, one_back = one_back, current
        return one_back
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Minimizing cumulative toll/fee costs when routing through checkpoints with variable fees.
- Optimizing energy consumption in step-based robotic or elevator movement control.
- Choosing the cheapest sequence of upgrade steps in a versioned migration path.

## Key Takeaway

This is the same "step 1 or 2" pattern as Climbing Stairs, but minimizing cost instead of counting paths — same recurrence shape, different combining operation (min instead of sum).
