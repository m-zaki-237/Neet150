# 99. Climbing Stairs

## Problem

You are climbing a staircase with `n` steps. Each time you can climb either 1 or 2 steps. Count the number of distinct ways you can reach the top.

## Example 1

### Input

```text
n = 2
```

### Expected Output

```text
2
```

### Explanation

You can go 1+1 or 2. That's 2 ways.

## Example 2

### Input

```text
n = 3
```

### Expected Output

```text
3
```

### Explanation

You can go 1+1+1, 1+2, or 2+1. That's 3 ways.

## How to Solve

### Key Idea

The number of ways to reach step `n` is the sum of the ways to reach step `n-1` and step `n-2`, because your last move was either a 1-step or a 2-step. This is exactly the Fibonacci sequence.

### Approach

1. Handle small cases directly (n = 1 or n = 2).
2. Keep two variables tracking the number of ways to reach the previous two steps.
3. Slide these two variables forward until you reach step `n`.

### Why It Works

Every way to reach step `n` ends with either a final single step from `n-1`, or a final double step from `n-2`. Adding those two counts covers all possibilities exactly once.

## Visual Explanation

```text
n = 2
dp[0] = 1   (base: one way - take no steps)
dp[1] = 1   (base: one way - a single 1-step)
dp[2] = dp[1] + dp[0] = 1 + 1 = 2   <- matches expected output
```

## Optimal Solution

```python
class Solution:
    def climbStairs(self, n: int) -> int:
        if n <= 2:
            return n

        one_step_before, two_steps_before = 2, 1
        for _ in range(3, n + 1):
            one_step_before, two_steps_before = one_step_before + two_steps_before, one_step_before
        return one_step_before
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Counting possible tiling/pattern combinations in layout or UI grid engines.
- Estimating paths in step-based workflows, such as automated build/release pipelines with fixed step sizes.
- Modeling combinatorial growth in network routing where hops can be 1 or 2 nodes.

## Key Takeaway

Whenever the answer for `n` depends on the answers for a fixed number of smaller subproblems, you can roll the state forward with a few variables instead of storing a whole array.
