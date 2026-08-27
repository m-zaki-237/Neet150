# 133. Meeting Rooms

## Problem

Given a list of meeting time intervals where each is `[start, end]`, determine if a single person could attend every meeting — meaning no two meetings overlap in time.

## Example 1

### Input

```text
intervals = [[0,30],[5,10],[15,20]]
```

### Expected Output

```text
false
```

### Explanation

`[0,30]` overlaps with both `[5,10]` and `[15,20]`, so one person cannot attend all of them.

## Example 2

### Input

```text
intervals = [[7,10],[2,4]]
```

### Expected Output

```text
true
```

### Explanation

`[2,4]` ends before `[7,10]` starts, so there is no overlap and one person can attend both.

## How to Solve

### Key Idea

If you sort the meetings by start time, a person can attend all of them only if each meeting starts after (or exactly when) the previous one ends. Any overlap means a conflict.

### Approach

1. Sort the intervals by start time.
2. Loop through consecutive pairs of meetings.
3. If any meeting starts before the previous one ends, return `false`.
4. If you get through the whole list without conflicts, return `true`.

### Why It Works

Sorting by start time means any overlap must show up between two neighboring meetings in the sorted order — you never need to compare a meeting to one further away, since that meeting starts even later.

## Visual Explanation

```text
Intervals: [0,30] [5,10] [15,20]
Timeline:  0------------------30
                5--10
                       15--20
Overlap:   [5,10] and [15,20] both fall inside [0,30] -> conflict -> false
```

## Optimal Solution

```python
class Solution:
    def canAttendMeetings(self, intervals: list[list[int]]) -> bool:
        intervals.sort(key=lambda pair: pair[0])

        for i in range(1, len(intervals)):
            if intervals[i][0] < intervals[i - 1][1]:
                return False

        return True
```

## Complexity

- **Time:** O(n log n) — dominated by sorting.
- **Space:** O(1) extra space (ignoring the sort's own space usage).

## Real-World Uses

- Checking whether a single employee's calendar has any double-booked meetings.
- Detecting conflicting reservations for a single shared resource, like a projector or car.
- Validating that a proposed schedule of tasks for one worker never overlaps.

## Key Takeaway

Sorting by start time is the fastest way to check for any overlap at all — this simple check is the foundation for harder problems like Meeting Rooms II.
