# 109. Longest Increasing Subsequence

## Problem

Given an integer array `nums`, find the length of the longest subsequence where the values are strictly increasing. The elements of the subsequence don't need to be next to each other in the original array, but they must keep their relative order.

## Example 1

### Input

```text
nums = [10, 9, 2, 5, 3, 7, 101, 18]
```

### Expected Output

```text
4
```

### Explanation

One longest increasing subsequence is [2, 3, 7, 18], which has length 4.

## Example 2

### Input

```text
nums = [7, 7, 7, 7]
```

### Expected Output

```text
1
```

### Explanation

Since the sequence must be strictly increasing, only a single element can be picked.

## How to Solve

### Key Idea

Keep a growing list that represents the smallest possible "tail" value for an increasing subsequence of each length seen so far. For each new number, either it extends the longest subsequence found so far, or it replaces an existing tail to keep future extensions easier (this is the patience-sorting trick).

### Approach

1. Keep a list `tails`, where `tails[k]` is the smallest possible tail value of any increasing subsequence of length `k + 1` found so far.
2. For each number in the array, binary search `tails` for the first position where a value greater than or equal to it exists.
3. If no such position exists, append the number (it extends the longest subsequence by one).
4. Otherwise, replace the value at that position with the current number (keeps future tails as small as possible without changing the count).
5. The final length of `tails` is the answer.

### Why It Works

Replacing a tail with a smaller-or-equal value never makes the subsequence length smaller, but it does make it easier for future numbers to extend that subsequence. The length of `tails` always equals the length of the longest increasing subsequence found among the numbers processed so far, even though `tails` itself may not be an actual subsequence from the array.

## Visual Explanation

```text
nums:  10  9  2  5  3  7 101  18
tails: [2, 3, 7, 18]   <- smallest tail per subsequence length
```

## Optimal Solution

```python
from bisect import bisect_left

class Solution:
    def lengthOfLIS(self, nums: list[int]) -> int:
        tails: list[int] = []

        for num in nums:
            pos = bisect_left(tails, num)
            if pos == len(tails):
                tails.append(num)
            else:
                tails[pos] = num

        return len(tails)
```

## Complexity

- **Time:** O(n log n)
- **Space:** O(n)

## Real-World Uses

- Finding the longest chain of compatible software versions or patches over time.
- Detecting the longest upward stock price trend within a sequence of daily prices.
- Identifying the longest sequence of increasing performance metrics across releases.

## Key Takeaway

The "patience sorting" trick turns an O(n^2) DP into an O(n log n) solution by maintaining the smallest possible tail for each subsequence length and using binary search to place each new number quickly.
