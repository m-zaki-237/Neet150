# 114. Coin Change II

## Problem

You are given an integer `amount` and an array `coins` representing different coin denominations, each available in unlimited supply. Count the number of distinct combinations (order does not matter) of coins that add up to `amount`.

## Example 1

### Input

```text
amount = 5, coins = [1,2,5]
```

### Expected Output

```text
4
```

### Explanation

The 4 combinations are: 5=5, 2+2+1, 2+1+1+1, 1+1+1+1+1.

## Example 2

### Input

```text
amount = 3, coins = [2]
```

### Expected Output

```text
0
```

### Explanation

No combination of the coin [2] sums to 3.

## How to Solve

### Key Idea

Think of it as a 2D grid where rows are coins considered so far and columns are amounts from 0 to target. For each coin, you can either skip it or use it (possibly multiple times), and you build up the count of ways for each amount.

### Approach

1. Create `dp` of size `(amount + 1)`, with `dp[0] = 1` (one way to make 0: use no coins).
2. For each coin, loop through amounts from `coin` to `amount` in increasing order, and add `dp[a - coin]` to `dp[a]`.
3. Processing one coin fully before moving to the next coin ensures combinations, not permutations, are counted.
4. Return `dp[amount]`.

### Why It Works

Looping coins in the outer loop and amounts in the inner loop guarantees each combination of coins is only counted once (in a fixed coin order), rather than counting the same set of coins in different orders separately.

## Visual Explanation

```text
amount = 5, coins = [1,2,5]

dp index:                  0  1  2  3  4  5
after processing coin=1:   1  1  1  1  1  1
after processing coin=2:   1  1  2  2  3  3   (dp[a] += dp[a-2])
after processing coin=5:   1  1  2  2  3  4   (dp[a] += dp[a-5])

dp[5] = 4  <- matches Example 1's output
```

## Optimal Solution

```python
from typing import List

class Solution:
    def change(self, amount: int, coins: List[int]) -> int:
        dp = [0] * (amount + 1)
        dp[0] = 1

        for coin in coins:
            for a in range(coin, amount + 1):
                dp[a] += dp[a - coin]

        return dp[amount]
```

## Complexity

- **Time:** O(amount * len(coins))
- **Space:** O(amount)

## Real-World Uses

- Computing the number of ways to make change with a fixed set of currency denominations.
- Counting valid combinations of items (e.g. postage stamps) that sum to a target value.
- Resource-allocation planning where quantities of a few resource types must sum to a budget.

## Key Takeaway

When counting combinations (not permutations), put the "item" loop on the outside and the "capacity" loop on the inside — this is a key trick distinguishing combination-counting DP from permutation-counting DP.
