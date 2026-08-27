# 132. Non-Overlapping Intervals

## Problem

Given a collection of intervals, find the minimum number of intervals you need to remove so that the rest of the intervals do not overlap with each other. Intervals that only touch at the edges (like `[1,2]` and `[2,3]`) are not considered overlapping.

## Example 1

### Input

```text
intervals = [[1,2],[2,3],[3,4],[1,3]]
```

### Expected Output

```text
1
```

### Explanation

Removing `[1,3]` leaves `[1,2],[2,3],[3,4]`, which do not overlap.

## Example 2

### Input

```text
intervals = [[1,2],[1,2],[1,2]]
```

### Expected Output

```text
2
```

### Explanation

You need to remove two of the three identical intervals to leave only one non-overlapping interval.

## How to Solve

### Key Idea

This is really "maximize the number of intervals you can keep without overlaps," which is the classic activity selection problem: always keep the interval that finishes earliest, because it leaves the most room for future intervals.

### Approach

1. Sort the intervals by their end value.
2. Keep track of the end time of the last interval you decided to keep (start with negative infinity).
3. Go through intervals in order: if the current interval's start is `>=` the last kept end, keep it and update the last end; otherwise, it overlaps, so count it as removed.
4. Return the count of removed intervals.

### Why It Works

Sorting by end time and greedily keeping the earliest-finishing non-conflicting interval is provably optimal for this kind of scheduling problem — it always leaves the maximum possible room for the intervals that come after.

## Visual Explanation

```text
Intervals: [1,2] [2,3] [3,4] [1,3]
Timeline:  1-2
              2-3
                 3-4
           1----3   <- removed (overlaps [2,3])
Kept:      1-2  2-3  3-4
```

## Optimal Solution

```python
class Solution:
    def eraseOverlapIntervals(self, intervals: list[list[int]]) -> int:
        intervals.sort(key=lambda pair: pair[1])

        removed = 0
        last_end = float("-inf")

        for start, end in intervals:
            if start >= last_end:
                last_end = end
            else:
                removed += 1

        return removed
```

## Complexity

- **Time:** O(n log n) — dominated by sorting.
- **Space:** O(1) extra space (ignoring the sort's own space usage).

## Real-World Uses

- Selecting the maximum number of non-conflicting meetings a single room can host.
- Scheduling the most video ads that can play in a time slot without overlapping.
- Picking the largest set of non-overlapping shifts a worker can take from an offered list.

## Key Takeaway

When you need to pick the maximum number of non-conflicting intervals, sort by end time and greedily keep the earliest finisher — this "greedy by end time" trick solves a whole family of scheduling problems.
