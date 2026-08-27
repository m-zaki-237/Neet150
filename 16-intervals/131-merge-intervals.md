# 131. Merge Intervals

## Problem

Given a collection of intervals where each is `[start, end]`, merge all intervals that overlap and return a list of the non-overlapping intervals that cover all the ranges in the input. The intervals are not necessarily sorted.

## Example 1

### Input

```text
intervals = [[1,3],[2,6],[8,10],[15,18]]
```

### Expected Output

```text
[[1,6],[8,10],[15,18]]
```

### Explanation

`[1,3]` and `[2,6]` overlap, so they merge into `[1,6]`. The rest do not overlap with anything.

## Example 2

### Input

```text
intervals = [[1,4],[4,5]]
```

### Expected Output

```text
[[1,5]]
```

### Explanation

`[1,4]` and `[4,5]` touch at 4, which counts as overlapping, so they merge into `[1,5]`.

## How to Solve

### Key Idea

If you sort the intervals by their start value, any intervals that overlap will end up next to each other. Then you just sweep through and merge neighbors that overlap.

### Approach

1. Sort the intervals by start time.
2. Start a result list with the first interval.
3. For each next interval, compare it with the last interval added to the result: if it starts before (or exactly when) the last one ends, merge them by extending the end; otherwise, add it as a new separate interval.

### Why It Works

Sorting guarantees that any interval that could overlap with the current merged interval must come right after it. So we never need to look back further than the last interval in the result.

## Visual Explanation

```text
Intervals: [1,3] [2,6] [8,10] [15,18]
Timeline:  1--3
              2-----6
                        8--10
                                 15---18
Merged:    1-----6      8--10    15---18
```

## Optimal Solution

```python
class Solution:
    def merge(self, intervals: list[list[int]]) -> list[list[int]]:
        intervals.sort(key=lambda pair: pair[0])
        result = [intervals[0]]

        for start, end in intervals[1:]:
            last_end = result[-1][1]
            if start <= last_end:
                result[-1][1] = max(last_end, end)
            else:
                result.append([start, end])

        return result
```

## Complexity

- **Time:** O(n log n) — dominated by sorting.
- **Space:** O(n) — for the sorted copy and result list.

## Real-World Uses

- Merging overlapping time ranges in server logs to find continuous outage windows.
- Combining overlapping booking or reservation slots into a single occupied range.
- Collapsing overlapping date ranges in a billing system into consolidated invoice periods.

## Key Takeaway

Sorting by start time turns "find overlaps anywhere" into "compare neighbors" — this sort-then-sweep pattern shows up in almost every interval problem.
