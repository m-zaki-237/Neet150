# 130. Insert Interval

## Problem

You are given a list of non-overlapping intervals sorted by their start time, and a new interval to add. Insert the new interval into the list so that the list is still sorted and no two intervals overlap, merging intervals as needed. Each interval is written as `[start, end]`.

## Example 1

### Input

```text
intervals = [[1,3],[6,9]], newInterval = [2,5]
```

### Expected Output

```text
[[1,5],[6,9]]
```

### Explanation

`[2,5]` overlaps with `[1,3]`, so they merge into `[1,5]`. `[6,9]` does not overlap, so it stays as is.

## Example 2

### Input

```text
intervals = [[1,2],[3,5],[6,7],[8,10],[12,16]], newInterval = [4,8]
```

### Expected Output

```text
[[1,2],[3,10],[12,16]]
```

### Explanation

`[4,8]` overlaps with `[3,5]`, `[6,7]`, and `[8,10]`, merging them all into `[3,10]`.

## How to Solve

### Key Idea

Walk through the intervals once. Any interval that ends before the new one starts goes straight to the result. Any interval that starts after the new one ends also goes straight to the result. Everything in between overlaps the new interval, so merge them all together.

### Approach

1. Add every interval that ends before `newInterval` starts to the result unchanged.
2. While the current interval overlaps `newInterval` (its start is `<=` the new interval's end), stretch `newInterval` to cover both by taking the min of the starts and the max of the ends.
3. Add the (possibly stretched) `newInterval` to the result, then add all remaining intervals unchanged.

### Why It Works

Since the input is already sorted and non-overlapping, once you pass the overlapping section you can never come back to it. A single left-to-right pass is enough to know exactly which intervals merge with the new one.

## Visual Explanation

```text
Intervals: [1,3] [6,9]   newInterval: [2,5]
Timeline:  1--3
                    6--9
New:          2-----5
Merged:    1-----5  6--9
```

## Optimal Solution

```python
class Solution:
    def insert(self, intervals: list[list[int]], newInterval: list[int]) -> list[list[int]]:
        result = []
        i, n = 0, len(intervals)
        start, end = newInterval

        # 1. Add all intervals ending before the new one starts
        while i < n and intervals[i][1] < start:
            result.append(intervals[i])
            i += 1

        # 2. Merge all overlapping intervals
        while i < n and intervals[i][0] <= end:
            start = min(start, intervals[i][0])
            end = max(end, intervals[i][1])
            i += 1
        result.append([start, end])

        # 3. Add the rest
        while i < n:
            result.append(intervals[i])
            i += 1

        return result
```

## Complexity

- **Time:** O(n) — each interval is visited once.
- **Space:** O(n) — for the result list (excluding output).

## Real-World Uses

- Adding a new booking to an already-sorted calendar and merging it with adjacent bookings.
- Inserting a new maintenance window into a sorted list of server downtime slots.
- Adding a new road closure to a sorted list of closures along a route and merging overlapping stretches.

## Key Takeaway

When intervals are already sorted, you almost never need to sort again — a single pass with a "before / overlapping / after" split handles insertion and merging cleanly.
