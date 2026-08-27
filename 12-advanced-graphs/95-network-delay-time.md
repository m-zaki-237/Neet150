# 95. Network Delay Time

## Problem

You are given a network of `n` nodes labeled `1` to `n`, and a list of directed weighted edges `times = [[u, v, w]]` meaning it takes `w` time for a signal to travel from node `u` to node `v`. Starting from node `k`, find the minimum time for a signal to reach all nodes. If some node can't be reached, return -1.

## Example 1

### Input

```text
times = [[2,1,1],[2,3,1],[3,4,1]], n = 4, k = 2
```

### Expected Output

```text
2
```

### Explanation

From node 2, it takes 1 to reach node 1 or 3, then 1 more to reach node 4 from 3, so the slowest node takes 2 total.

## Example 2

### Input

```text
times = [[1,2,1]], n = 2, k = 1
```

### Expected Output

```text
1
```

### Explanation

Node 1 sends the signal to node 2, taking 1 unit of time, and both nodes are now reached.

## How to Solve

### Key Idea

This is a shortest-path problem with non-negative edge weights, which is exactly what Dijkstra's algorithm is built for. We want the shortest time from `k` to every other node, then answer is the maximum of those shortest times.

### Approach

1. Build an adjacency list from the edges.
2. Use a min-heap starting with `(0, k)`, and a dictionary to track the shortest known time to reach each node.
3. Pop the closest unvisited node, record its time, and push its neighbors with updated cumulative times.
4. After the heap is empty, if all `n` nodes were reached, return the max recorded time; otherwise return -1.

### Why It Works

Dijkstra's algorithm always expands the closest known unvisited node next, so once a node is popped from the heap its shortest distance is finalized. Taking the max of all finalized distances tells us when the last (slowest) node receives the signal.

## Visual Explanation

```text
Min-heap pops, in order, with the finalized shortest time to each node:

  pop (time=0, node=2)  -> finalize dist[2]=0  -> push (1, 1), push (1, 3)
  pop (time=1, node=1)  -> finalize dist[1]=1  -> no new (unvisited) neighbors
  pop (time=1, node=3)  -> finalize dist[3]=1  -> push (2, 4)
  pop (time=2, node=4)  -> finalize dist[4]=2  -> no neighbors

All 4 nodes reached; answer = max(0, 1, 1, 2) = 2
```

## Optimal Solution

```python
import heapq
from collections import defaultdict

class Solution:
    def networkDelayTime(self, times: list[list[int]], n: int, k: int) -> int:
        graph = defaultdict(list)
        for u, v, w in times:
            graph[u].append((v, w))

        min_heap = [(0, k)]
        visited = {}

        while min_heap:
            time, node = heapq.heappop(min_heap)
            if node in visited:
                continue
            visited[node] = time

            for neighbor, weight in graph[node]:
                if neighbor not in visited:
                    heapq.heappush(min_heap, (time + weight, neighbor))

        if len(visited) != n:
            return -1
        return max(visited.values())
```

## Complexity

- **Time:** O(E log V), where E is edges and V is nodes (standard Dijkstra with a heap)
- **Space:** O(V + E) for the graph, heap, and visited map

## Real-World Uses

- Network routing and latency estimation, finding how long it takes signals to propagate across infrastructure.
- Broadcast and messaging systems that need to know when the last subscriber will receive an update.
- Epidemiological or rumor-spread models estimating time for information to reach an entire population.

## Key Takeaway

Any "shortest time/distance to reach everything from one source" problem with non-negative weights is a textbook Dijkstra's algorithm application using a min-heap.
