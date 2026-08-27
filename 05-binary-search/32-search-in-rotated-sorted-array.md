# 32. Search in Rotated Sorted Array

## Problem

You are given a sorted array of unique integers that has been rotated at some unknown pivot, and a target value. Return the index of the target if it exists, otherwise return -1. Your solution should run in logarithmic time.

## Example 1

### Input

```text
nums = [4,5,6,7,0,1,2], target = 0
```

### Expected Output

```text
4
```

### Explanation

0 is located at index 4 in the rotated array.

## Example 2

### Input

```text
nums = [4,5,6,7,0,1,2], target = 3
```

### Expected Output

```text
-1
```

### Explanation

3 does not appear anywhere in the array.

## How to Solve

### Key Idea

At every step, at least one half of the array (split at mid) is guaranteed to be normally sorted. Figure out which half is sorted, then check if the target falls within that half's range to decide where to search next.

### Approach

1. Set `left = 0` and `right = len(nums) - 1`. While `left <= right`, compute `mid`; if `nums[mid] == target`, return `mid`.
2. Determine which side is sorted by comparing `nums[left]` and `nums[mid]`. If the left side is sorted, check whether the target lies within `nums[left]` to `nums[mid]`.
3. If the target is in that sorted range, search there (`right = mid - 1`); otherwise search the other side (`left = mid + 1`). Do the mirrored check when the right side is sorted instead.

### Why It Works

A rotated sorted array always has one contiguous sorted half around any midpoint. Knowing that half's range lets you decide in O(1) whether the target could be there, letting you discard the other half safely.

## Visual Explanation

```text
nums = [4,5,6,7,0,1,2], target = 0
 L           M        R    -> mid=7, left half [4..7] sorted, target 0 not in [4,7), search right (left=mid+1)
             L   M    R    -> mid=5, nums[mid]=1, left half [0,1] sorted, target 0 in [0,1), search left (right=mid-1)
             LM             -> mid=4, nums[mid]=0 == target -> found!
```

## Optimal Solution

```python
class Solution:
    def search(self, nums: list[int], target: int) -> int:
        left, right = 0, len(nums) - 1

        while left <= right:
            mid = (left + right) // 2

            if nums[mid] == target:
                return mid

            if nums[left] <= nums[mid]:
                # left half is sorted
                if nums[left] <= target < nums[mid]:
                    right = mid - 1
                else:
                    left = mid + 1
            else:
                # right half is sorted
                if nums[mid] < target <= nums[right]:
                    left = mid + 1
                else:
                    right = mid - 1

        return -1
```

## Complexity

- **Time:** O(log n)
- **Space:** O(1)

## Real-World Uses

- Searching logs or data in a wrapped circular buffer without unrolling it first.
- Querying a rotated/sharded sorted dataset (e.g., a ring-partitioned key space in distributed systems).
- Finding entries in a playlist or schedule that wraps around (e.g., a rotated weekly roster).

## Key Takeaway

When an array is "sorted with a twist," look for the half that is still normally sorted at each step — that half gives you a reliable range check to guide the binary search.
