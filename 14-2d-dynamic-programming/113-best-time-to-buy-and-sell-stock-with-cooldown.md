# 113. Best Time to Buy and Sell Stock With Cooldown

## Problem

You are given an array `prices` where `prices[i]` is the price of a stock on day `i`. You may buy and sell as many times as you like, but after selling you must wait one full day (a cooldown) before buying again, and you can hold at most one share at a time. Find the maximum profit possible.

## Example 1

### Input

```text
prices = [1,2,3,0,2]
```

### Expected Output

```text
3
```

### Explanation

Buy on day 0 (price 1), sell on day 1 (price 2), cooldown on day 2, buy on day 3 (price 0), sell on day 4 (price 2). Profit = 1 + 2 = 3.

## Example 2

### Input

```text
prices = [1]
```

### Expected Output

```text
0
```

### Explanation

With only one day, you cannot complete any transaction, so profit is 0.

## How to Solve

### Key Idea

On any given day you are in one of three states: holding a stock, just sold (in cooldown), or free to buy (sold long ago or never bought). Track the best profit for each state as you move day by day; this is a 2D DP over (day, state).

### Approach

1. Define three running values: `hold` (max profit while holding a stock), `sold` (max profit right after selling today), and `rest` (max profit while not holding and not in cooldown).
2. Initialize `hold = -prices[0]`, `sold = 0`, `rest = 0`.
3. For each day, compute new values from the previous day's values: `new_hold = max(hold, rest - price)`, `new_sold = hold + price`, `new_rest = max(rest, sold)`.
4. Update `hold, sold, rest` to the new values after each day.
5. The answer is `max(sold, rest)` at the end.

### Why It Works

Each state transition only depends on the previous day's three states, which is exactly a 2D DP table of days versus states, just computed with rolling variables instead of a full grid to save space.

## Visual Explanation

```text
prices:      1     2     3     0     2
day:         0     1     2     3     4
hold:       -1    -1    -1     1     1
sold:        0     1     2    -1     3
rest:        0     0     1     2     2

hold[day] = max(hold[day-1], rest[day-1] - price)
sold[day] = hold[day-1] + price
rest[day] = max(rest[day-1], sold[day-1])

answer = max(sold[4], rest[4]) = max(3, 2) = 3  <- matches Example 1's output
```

## Optimal Solution

```python
from typing import List

class Solution:
    def maxProfit(self, prices: List[int]) -> int:
        if not prices:
            return 0

        hold = -prices[0]
        sold = 0
        rest = 0

        for price in prices[1:]:
            prev_hold, prev_sold, prev_rest = hold, sold, rest
            hold = max(prev_hold, prev_rest - price)
            sold = prev_hold + price
            rest = max(prev_rest, prev_sold)

        return max(sold, rest)
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Algorithmic trading strategies that model mandatory cooldown periods after a trade.
- Inventory or resource management systems with a "recharge" delay between actions.
- State-machine based scheduling where an action locks out the next immediate action.

## Key Takeaway

When a problem has a small set of "states" per step (hold/sold/rest), model it as a 2D DP of step versus state; you can often collapse it to rolling variables for O(1) space.
