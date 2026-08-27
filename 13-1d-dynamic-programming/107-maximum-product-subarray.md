# 107. Maximum Product Subarray

## Problem

Given an integer array `nums`, find a contiguous subarray (at least one number) that has the largest product, and return that product.

## Example 1

### Input

```text
nums = [2, 3, -2, 4]
```

### Expected Output

```text
6
```

### Explanation

The subarray [2, 3] has the largest product, 6.

## Example 2

### Input

```text
nums = [-2, 0, -1]
```

### Expected Output

```text
0
```

### Explanation

The subarray [0] gives the largest product, since any subarray with -2 and -1 together can't be formed without crossing the 0, and single negatives give negative products.

## How to Solve

### Key Idea

Because multiplying by a negative number flips the sign, a small (very negative) running product can become the largest product if multiplied by another negative number. So track both the maximum and minimum running product at each step.

### Approach

1. Keep track of the current maximum product and current minimum product ending at the previous position, plus the best product found overall.
2. For each number, compute three candidates: the number itself, the number times the current max, and the number times the current min.
3. The new current max and current min are the largest and smallest of those three candidates (this handles sign flips correctly).
4. Update the overall best answer with the new current max after each step.

### Why It Works

A negative number turns the previous max into a small (negative) result and the previous min into a large (positive) result. By always tracking both extremes, you never lose track of a negative value that could later become the maximum once multiplied by another negative.

## Visual Explanation

```text
current_max[i] = max(nums[i], current_max[i-1]*nums[i], current_min[i-1]*nums[i])
current_min[i] = min(nums[i], current_max[i-1]*nums[i], current_min[i-1]*nums[i])
```

## Optimal Solution

```python
class Solution:
    def maxProduct(self, nums: list[int]) -> int:
        result = nums[0]
        current_max, current_min = nums[0], nums[0]

        for num in nums[1:]:
            candidates = (num, current_max * num, current_min * num)
            current_max, current_min = max(candidates), min(candidates)
            result = max(result, current_max)

        return result
```

## Complexity

- **Time:** O(n)
- **Space:** O(1)

## Real-World Uses

- Analyzing stock price movement windows where gains/losses compound multiplicatively.
- Detecting best-performing contiguous periods in signal or sensor data with sign-flipping values.
- Optimizing multiplicative scoring windows in risk or growth-rate analysis.

## Key Takeaway

When an operation (like multiplication) can flip the best value into the worst and vice versa, track both the running maximum and minimum, not just the maximum.
