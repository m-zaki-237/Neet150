# 102. House Robber II

## Problem

Same as House Robber, but now the houses are arranged in a circle: the first and last houses are considered adjacent. You still cannot rob two adjacent houses. Find the maximum amount of money you can rob.

## Example 1

### Input

```text
nums = [2, 3, 2]
```

### Expected Output

```text
3
```

### Explanation

Robbing house 0 and house 2 is not allowed since they're adjacent in the circle. The best is just house 1 (3).

## Example 2

### Input

```text
nums = [1, 2, 3, 1]
```

### Expected Output

```text
4
```

### Explanation

Rob house 0 and house 2 (1 + 3 = 4). Since it's circular, house 3 can't be combined with house 0, but this choice avoids that.

## How to Solve

### Key Idea

Since house 0 and the last house can't both be robbed, split the problem into two simpler non-circular cases: rob houses [0 .. n-2], or rob houses [1 .. n-1]. Run the regular House Robber logic on each and take the better result.

### Approach

1. If there's only one house, return its value directly.
2. Run the linear House Robber DP on the array excluding the last house.
3. Run the linear House Robber DP on the array excluding the first house.
4. Return the maximum of the two results.

### Why It Works

Any valid selection either excludes house 0 or excludes the last house (it can't include both, since they're adjacent in the circle). So the best circular answer is the better of the two linear sub-answers, each of which is solvable with the standard House Robber DP.

## Visual Explanation

```text
nums = [2, 3, 2]
Case A (exclude last):  [2, 3]  -> linear House Robber = 3
Case B (exclude first): [3, 2]  -> linear House Robber = 3
answer = max(3, 3) = 3   <- matches expected output
```

## Optimal Solution

```python
class Solution:
    def rob(self, nums: list[int]) -> int:
        if len(nums) == 1:
            return nums[0]

        def rob_linear(houses: list[int]) -> int:
            rob_prev, rob_prev_prev = 0, 0
            for num in houses:
                rob_prev, rob_prev_prev = max(rob_prev_prev + num, rob_prev), rob_prev
            return rob_prev

        return max(rob_linear(nums[:-1]), rob_linear(nums[1:]))
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Scheduling resources arranged in a circular rotation, like round-the-clock shift patterns.
- Allocating booths or stalls in a circular market layout with adjacency restrictions.
- Optimizing selections in cyclic network topologies (e.g., ring networks) where endpoints are neighbors.

## Key Takeaway

When a circular constraint links the first and last elements, try breaking it into two linear versions of a problem you already know how to solve, then combine the results.
