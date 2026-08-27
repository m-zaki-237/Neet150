# 120. Burst Balloons

## Problem

You are given `n` balloons in a row, each with a number on it, given as array `nums`. Bursting a balloon `i` earns `nums[left] * nums[i] * nums[right]` coins, where `left` and `right` are the currently adjacent balloons (treat out-of-bounds neighbors as the value 1). Find the maximum coins you can collect by bursting all the balloons in some order.

## Example 1

### Input

```text
nums = [3,1,1,5]
```

### Expected Output

```text
40
```

### Explanation

Burst the two 1's first, then the 3, then the 5: burst index 2 (`1*1*5=5`), then index 1 (`3*1*5=15`), then index 0 (`1*3*5=15`), then index 3 (`1*5*1=5`). Total = 5+15+15+5 = 40.

## Example 2

### Input

```text
nums = [1,5]
```

### Expected Output

```text
10
```

### Explanation

Burst the balloon with 1 first (neighbors are boundary 1 and balloon 5, giving 1*1*5=5), then burst the balloon with 5 (neighbors are boundary 1 and boundary 1, giving 1*5*1=5). Total = 10.

## How to Solve

### Key Idea

Instead of thinking about which balloon to burst first, think about which balloon to burst LAST within a range. That balloon's neighbors at burst time are the two boundary balloons of the range, which makes the subproblems independent — a classic 2D interval DP.

### Approach

1. Pad `nums` with a 1 at both ends to simplify boundary handling.
2. Define `dp[left][right]` as the maximum coins from bursting all balloons strictly between indices `left` and `right` (exclusive boundaries).
3. For each interval length, and each starting `left`, try every possible balloon `k` between `left` and `right` as the *last* one burst in that interval.
4. `dp[left][right] = max over k of dp[left][k] + dp[k][right] + nums[left]*nums[k]*nums[right]`.
5. The answer is `dp[0][n+1]` for the padded array of length `n+2`.

### Why It Works

By fixing the last balloon burst in a range, its final neighbors are guaranteed to be the range's boundary values, so the two sub-ranges on either side can be solved completely independently, letting us combine their optimal answers safely.

## Visual Explanation

Padded array: `[1, 3, 1, 1, 5, 1]` (indices 0-5). A few key filled-in cells for `nums=[3,1,1,5]`:

```text
dp[1][3] = 3   (burst index 2 last: balloons[1]*balloons[2]*balloons[3] = 3*1*1 = 3)
dp[2][4] = 5   (burst index 3 last: 1*1*5 = 5)
dp[1][4] = 20  (best: dp[1][2]+dp[2][4]+balloons[1]*balloons[2]*balloons[4] = 0+5+3*1*5 = 20)
dp[0][5] = 40  (final answer, combining all sub-ranges)
```

Each cell reuses two already-solved sub-ranges plus one multiplication for the "last burst" balloon `k`, which is exactly the recurrence: `dp[left][k] + dp[k][right] + nums[left]*nums[k]*nums[right] --> dp[left][right]`.

## Optimal Solution

```python
from typing import List

class Solution:
    def maxCoins(self, nums: List[int]) -> int:
        balloons = [1] + nums + [1]
        n = len(balloons)
        dp = [[0] * n for _ in range(n)]

        for length in range(2, n):
            for left in range(0, n - length):
                right = left + length
                best = 0
                for k in range(left + 1, right):
                    coins = balloons[left] * balloons[k] * balloons[right]
                    coins += dp[left][k] + dp[k][right]
                    best = max(best, coins)
                dp[left][right] = best

        return dp[0][n - 1]
```

## Complexity

- **Time:** O(n^3)
- **Space:** O(n^2)

## Real-World Uses

- Optimizing order of operations where each action's value depends on its current neighbors (e.g. demolition or removal sequencing).
- Resource extraction planning where removing an item's value depends on adjacent remaining items.
- Scheduling interdependent tasks where the reward for finishing a task depends on which other tasks are still active.

## Key Takeaway

When an operation's cost depends on shrinking neighbors, reframe the problem around the last action in a range (not the first) to make subranges independent — this is the core trick of interval DP.
