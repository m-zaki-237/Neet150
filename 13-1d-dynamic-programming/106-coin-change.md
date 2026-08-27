# 106. Coin Change

## Problem

You are given an array of coin denominations `coins` and a target amount. Find the minimum number of coins needed to make up that amount. You have an unlimited supply of each coin. If it's not possible to make the amount, return -1.

## Example 1

### Input

```text
coins = [1, 2, 5], amount = 11
```

### Expected Output

```text
3
```

### Explanation

11 = 5 + 5 + 1, which uses 3 coins.

## Example 2

### Input

```text
coins = [2], amount = 3
```

### Expected Output

```text
-1
```

### Explanation

You can't make 3 using only coins of value 2.

## How to Solve

### Key Idea

The fewest coins needed to make amount `a` is 1 plus the fewest coins needed to make `a - coin`, minimized over every coin you're allowed to use.

### Approach

1. Create an array `dp` of size `amount + 1`, where `dp[a]` will hold the minimum coins needed to make amount `a`. Initialize with a large "impossible" value, except `dp[0] = 0`.
2. For each amount from 1 to the target, try every coin: if the coin's value fits, update `dp[a] = min(dp[a], dp[a - coin] + 1)`.
3. After filling the table, check `dp[amount]`: if it's still the "impossible" value, return -1, otherwise return it.

### Why It Works

Any way to make amount `a` uses some coin as the "last coin added." Trying every coin as that last coin and taking the best result over all of them guarantees you find the true minimum, because smaller amounts are already solved optimally before larger ones are computed.

## Visual Explanation

```text
dp[a] = min over each coin c of ( dp[a - c] + 1 )
dp: [0, 1, 1, 2, 2, 1, 2, 2, 3, 3, 2, 3]
     0  1  2  3  4  5  6  7  8  9 10 11
```

## Optimal Solution

```python
class Solution:
    def coinChange(self, coins: list[int], amount: int) -> int:
        impossible = amount + 1
        dp = [impossible] * (amount + 1)
        dp[0] = 0

        for a in range(1, amount + 1):
            for coin in coins:
                if coin <= a:
                    dp[a] = min(dp[a], dp[a - coin] + 1)

        return dp[amount] if dp[amount] != impossible else -1
```

## Complexity

- **Time:** O(amount * number of coins)
- **Space:** O(amount)

## Real-World Uses

- Making change with the minimum number of currency denominations in POS systems.
- Minimizing the number of shipping boxes/containers needed to pack a given weight or volume.
- Choosing the fewest resource units (e.g., cloud instance sizes) to cover a workload target.

## Key Takeaway

This is the classic "unbounded knapsack" pattern: build up answers for every smaller amount first, and combine them by trying each choice (coin) as the last one used.
