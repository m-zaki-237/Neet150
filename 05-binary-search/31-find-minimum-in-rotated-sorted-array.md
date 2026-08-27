# 31. Find Minimum in Rotated Sorted Array

## Problem

You are given a sorted array of unique integers that has been rotated at some unknown pivot point (for example, `[0,1,2,4,5,6,7]` might become `[4,5,6,7,0,1,2]`). Find the minimum element in the array. Your solution should run faster than checking every element.

## Example 1

### Input

```text
nums = [3,4,5,1,2]
```

### Expected Output

```text
1
```

### Explanation

The array was rotated so that 1, the smallest element, ended up after 5.

## Example 2

### Input

```text
nums = [4,5,6,7,0,1,2]
```

### Expected Output

```text
0
```

### Explanation

0 is the smallest value in the rotated array.

## How to Solve

### Key Idea

Compare the middle element to the rightmost element. If the middle is greater than the rightmost, the minimum must be somewhere to the right of mid; otherwise, the minimum is at mid or to its left.

### Approach

1. Set `left = 0` and `right = len(nums) - 1`.
2. While `left < right`, compute `mid`. If `nums[mid] > nums[right]`, the minimum is in the right half, so set `left = mid + 1`.
3. Otherwise the minimum is in the left half including mid, so set `right = mid`. Repeat until `left == right`, which points to the minimum.

### Why It Works

Comparing against the rightmost element tells you which side of `mid` contains the "break" in sorted order (where the rotation happened), and the minimum always sits right at or after that break.

## Visual Explanation

```text
nums = [4,5,6,7,0,1,2]
 L           M        R    -> nums[mid]=7 > nums[right]=2, min is right of mid (left=mid+1)
             L   M    R    -> nums[mid]=1 <= nums[right]=2, min is at/left of mid (right=mid)
             L M  R        -> nums[mid]=0 <= nums[right]=1, right=mid
             LR            -> left==right -> min = 0
```

## Optimal Solution

```python
class Solution:
    def findMin(self, nums: list[int]) -> int:
        left, right = 0, len(nums) - 1

        while left < right:
            mid = (left + right) // 2

            if nums[mid] > nums[right]:
                left = mid + 1
            else:
                right = mid

        return nums[left]
```

## Complexity

- **Time:** O(log n)
- **Space:** O(1)

## Real-World Uses

- Finding the "rotation point" in a circular buffer or ring log to locate the oldest entry.
- Identifying the reset/rollover point in cyclic sensor or counter data.
- Locating the start of the current epoch in a rotated/wrapped time-series dataset.

## Key Takeaway

Even when an array isn't fully sorted, it can still have a monotonic property (like which half contains the rotation break) that lets you use a modified binary search.
