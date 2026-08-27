# 134. Meeting Rooms II

## Problem

Given a list of meeting time intervals `[start, end]`, find the minimum number of meeting rooms required so that all meetings can happen, possibly at the same time in different rooms.

## Example 1

### Input

```text
intervals = [[0,30],[5,10],[15,20]]
```

### Expected Output

```text
2
```

### Explanation

`[0,30]` overlaps with both other meetings, but `[5,10]` and `[15,20]` don't overlap each other, so 2 rooms are enough.

## Example 2

### Input

```text
intervals = [[7,10],[2,4]]
```

### Expected Output

```text
1
```

### Explanation

The two meetings don't overlap at all, so they can share a single room.

## How to Solve

### Key Idea

At any moment, the number of rooms you need equals the number of meetings currently in progress. So track how many meetings are "active" as time moves forward, and find the peak.

### Approach

1. Use a min-heap that stores the end time of each meeting currently using a room.
2. Sort meetings by start time.
3. For each meeting, first free up any room whose meeting has already ended (the heap's smallest end time is `<=` the current meeting's start) by popping it.
4. Push the current meeting's end time onto the heap (assign it a room, reusing a freed one if available, otherwise a new one).
5. The maximum size the heap ever reaches is the answer.

### Why It Works

The heap always holds the end times of meetings that are still "in use." By always checking the earliest-ending meeting first, we free up rooms as soon as possible, so the heap size at any point is exactly the minimum number of rooms needed for the meetings running at that time.

## Visual Explanation

```text
Intervals: [0,30] [5,10] [15,20]
Timeline:  0------------------30   (room 1)
                5--10               (room 2)
                       15--20       (room 2, reused after 10)
Peak overlap at any instant = 2 rooms needed
```

## Optimal Solution

```python
import heapq

class Solution:
    def minMeetingRooms(self, intervals: list[list[int]]) -> int:
        if not intervals:
            return 0

        intervals.sort(key=lambda pair: pair[0])
        rooms = []  # min-heap of end times currently in use
        max_rooms = 0

        for start, end in intervals:
            # Free any room whose meeting has already ended
            while rooms and rooms[0] <= start:
                heapq.heappop(rooms)

            heapq.heappush(rooms, end)
            max_rooms = max(max_rooms, len(rooms))

        return max_rooms
```

## Complexity

- **Time:** O(n log n) — sorting plus heap operations for each meeting.
- **Space:** O(n) — for the heap, in the worst case when all meetings overlap.

## Real-World Uses

- Determining how many conference rooms an office needs to satisfy all booked meetings.
- Calculating the minimum number of servers needed to handle overlapping request windows.
- Figuring out how many checkout lanes a store needs during peak overlapping customer visits.

## Key Takeaway

Whenever you need to track "how many things are active at once," a min-heap of end times (or a sweep-line over start/end events) is the go-to tool.
