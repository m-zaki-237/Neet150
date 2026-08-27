# 30. Koko Eating Bananas

## Problem

Koko has piles of bananas and `h` hours to eat them all before the guards return. She picks a speed `k` (bananas per hour) and eats from one pile per hour: if a pile has fewer than `k` bananas she finishes it and moves on that hour, otherwise she eats exactly `k` and the rest of the pile waits until the next hour. Find the minimum integer `k` so she can eat all the bananas within `h` hours.

## Example 1

### Input

```text
piles = [3,6,7,11], h = 8
```

### Expected Output

```text
4
```

### Explanation

At speed 4, the hours needed are 1+2+2+3 = 8, which exactly fits in 8 hours.

## Example 2

### Input

```text
piles = [30,11,23,4,20], h = 5
```

### Expected Output

```text
30
```

### Explanation

With only 5 hours and 5 piles, Koko must finish each pile in one hour, so she needs a speed at least as large as the biggest pile.

## How to Solve

### Key Idea

As eating speed increases, the total hours needed decreases (or stays the same). This monotonic relationship lets you binary search on the answer itself, the speed `k`, instead of searching through the piles directly.

### Approach

1. Set `left = 1` and `right = max(piles)`, since the fastest useful speed is finishing the biggest pile in one hour.
2. For a candidate speed `mid`, compute the total hours needed as the sum of `ceil(pile / mid)` for every pile.
3. If the hours needed fit within `h`, try a smaller speed by moving `right = mid`; otherwise the speed is too slow, so move `left = mid + 1`. Continue until `left == right`, which is the answer.

### Why It Works

The number of hours required is a non-increasing function of speed, so the set of valid speeds forms a contiguous range starting from some minimum value, which is exactly the shape binary search needs.

## Visual Explanation

```text
piles = [3,6,7,11], h = 8
speed range: [1 ................. 11]
              L         M          R    -> mid=6, hours=1+1+2+2=6 <= 8, try smaller (right=mid)
              L    M    R               -> mid=3, hours=1+2+3+4=10 > 8, too slow (left=mid+1)
                   L M  R               -> converge -> answer = 4
```

## Optimal Solution

```python
import math


class Solution:
    def minEatingSpeed(self, piles: list[int], h: int) -> int:
        left, right = 1, max(piles)

        while left < right:
            mid = (left + right) // 2
            hours = sum(math.ceil(pile / mid) for pile in piles)

            if hours <= h:
                right = mid
            else:
                left = mid + 1

        return left
```

## Complexity

- **Time:** O(n log m), where n is the number of piles and m is the largest pile size
- **Space:** O(1)

## Real-World Uses

- Binary-search-on-answer for capacity planning, e.g. finding minimum server throughput to meet an SLA.
- Rate-limiting: finding the minimum allowed request rate that still clears a queue in time.
- Resource sizing, such as the minimum number of workers/machines to finish a batch job by a deadline.

## Key Takeaway

When a problem asks for the minimum (or maximum) value that satisfies some condition, and increasing that value makes the condition easier (or harder) to satisfy, you can binary search directly on the answer.
