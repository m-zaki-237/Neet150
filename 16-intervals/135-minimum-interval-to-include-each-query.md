# 135. Minimum Interval to Include Each Query

## Problem

You are given a list of intervals `[start, end]` and a list of query values. For each query, find the size (`end - start + 1`) of the smallest interval that contains that query value. If no interval contains a query, the answer for it is `-1`.

## Example 1

### Input

```text
intervals = [[1,4],[2,4],[3,6],[4,4]], queries = [2,3,4,5]
```

### Expected Output

```text
[3,3,1,4]
```

### Explanation

For query 2: intervals `[1,4]` (size 4) and `[2,4]` (size 3) contain it, smallest is 3. For query 4: `[4,4]` (size 1) contains it and is smallest. For query 5: only `[3,6]` (size 4) contains it.

## Example 2

### Input

```text
intervals = [[2,3],[2,5],[1,8],[20,25]], queries = [2,19,5,22]
```

### Expected Output

```text
[2,-1,4,6]
```

### Explanation

For query 19: no interval contains it, so the answer is `-1`. For query 22: only `[20,25]` (size 6) contains it.

## How to Solve

### Key Idea

Process both intervals and queries in sorted order by their start/value. As the query value grows, add all intervals that have "started" (start `<=` query) into a min-heap keyed by interval size, and remove intervals from the heap that have already "ended" (end `<` query) since they can never help any query from now on.

### Approach

1. Sort intervals by start value. Sort queries but remember their original index so the answer can be placed back correctly.
2. Use a min-heap storing `(size, end)` for intervals that have started.
3. For each query in increasing order: push all intervals whose start is `<=` the query value into the heap.
4. Pop from the heap while its top interval's end is `<` the query value (it's expired and useless for this or any later query).
5. If the heap is non-empty, the top gives the smallest valid interval size for this query; otherwise the answer is `-1`.
6. Store answers using the saved original query indices.

### Why It Works

Because queries are processed in increasing order, once an interval's end is smaller than the current query value, it will never contain any future (larger) query either, so it's safe to discard permanently. The min-heap always keeps the smallest surviving interval size on top, giving an efficient answer per query.

## Visual Explanation

```text
Intervals: [1,4] [2,4] [3,6] [4,4]
Timeline:  1-----4
              2--4
                 3-----6
                    4-4
Query 4:   candidates ending >= 4 -> [1,4](4) [2,4](3) [3,6](4) [4,4](1)
           smallest containing interval size = 1 ([4,4])
```

## Optimal Solution

```python
import heapq

class Solution:
    def minInterval(self, intervals: list[list[int]], queries: list[int]) -> list[int]:
        intervals.sort(key=lambda pair: pair[0])
        sorted_queries = sorted(range(len(queries)), key=lambda i: queries[i])

        answers = [-1] * len(queries)
        heap = []  # (size, end)
        i = 0
        n = len(intervals)

        for q_index in sorted_queries:
            q = queries[q_index]

            # Add all intervals that have started by this query
            while i < n and intervals[i][0] <= q:
                start, end = intervals[i]
                heapq.heappush(heap, (end - start + 1, end))
                i += 1

            # Remove intervals that have already ended before this query
            while heap and heap[0][1] < q:
                heapq.heappop(heap)

            if heap:
                answers[q_index] = heap[0][0]

        return answers
```

## Complexity

- **Time:** O((n + q) log n) — sorting plus heap pushes/pops for intervals and queries.
- **Space:** O(n + q) — for the heap and the sorted query index list.

## Real-World Uses

- Assigning the smallest available IP subnet or address block that covers a requested address.
- Finding the tightest-fitting warehouse storage bin that can hold an item of a given size.
- Allocating the smallest matching server capacity tier to handle a resource request at a given time.

## Key Takeaway

Sorting queries and intervals together, then using a min-heap to track "currently valid" intervals, is a powerful pattern for answering many point-in-interval questions efficiently instead of checking each query against every interval.
