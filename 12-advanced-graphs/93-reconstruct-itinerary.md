# 93. Reconstruct Itinerary

## Problem

You are given a list of airline tickets, each ticket is a pair `[from, to]`. Starting from `"JFK"`, reconstruct the itinerary that uses every ticket exactly once. If there are multiple valid itineraries, return the one that reads smallest in lexical (alphabetical) order. Assume all tickets form at least one valid itinerary.

## Example 1

### Input

```text
tickets = [["MUC","LHR"],["JFK","MUC"],["LHR","SFO"],["SFO","SJC"]]
```

### Expected Output

```text
["JFK","MUC","LHR","SFO","SJC"]
```

### Explanation

There is only one way to use all tickets starting from JFK, visiting each city in this order.

## Example 2

### Input

```text
tickets = [["JFK","SFO"],["JFK","ATL"],["SFO","ATL"],["ATL","JFK"],["ATL","SFO"]]
```

### Expected Output

```text
["JFK","ATL","JFK","SFO","ATL","SFO"]
```

### Explanation

Two paths use all 5 tickets, but this one is smaller lexically because "ATL" comes before "SFO" as the second stop.

## How to Solve

### Key Idea

Think of each airport as a node and each ticket as a directed edge. We need to walk through every edge exactly once — this is an Eulerian path problem, solved cleanly with Hierholzer's algorithm.

### Approach

1. Build an adjacency map from each departure airport to a sorted (min-heap) list of destination airports.
2. Do a DFS from "JFK": at each airport, repeatedly pop the smallest unused destination and recurse into it, removing the ticket as you use it.
3. When an airport has no more outgoing tickets left, add it to the result path.
4. Reverse the result path at the end — this "post-order" trick correctly places dead-end detours before continuing the main route.

### Why It Works

By always trying the lexically smallest destination first and only "finishing" an airport once all its tickets are used, we guarantee that any dead-end loops get inserted in the correct position when we reverse the post-order list. This is exactly how Hierholzer's algorithm finds an Eulerian path.

## Visual Explanation

```text
DFS call stack (pop smallest unused destination each time):
  dfs(JFK) -> pops MUC
    dfs(MUC) -> pops LHR
      dfs(LHR) -> pops SFO
        dfs(SFO) -> pops SJC
          dfs(SJC) -> no tickets left -> append "SJC"
        no tickets left -> append "SFO"
      no tickets left -> append "LHR"
    no tickets left -> append "MUC"
  no tickets left -> append "JFK"

Post-order route (built bottom-up): [SJC, SFO, LHR, MUC, JFK]
Reversed (final answer):            [JFK, MUC, LHR, SFO, SJC]
```

## Optimal Solution

```python
from collections import defaultdict
import heapq

class Solution:
    def findItinerary(self, tickets: list[list[str]]) -> list[str]:
        graph = defaultdict(list)
        for src, dst in tickets:
            heapq.heappush(graph[src], dst)

        route = []

        def dfs(airport):
            while graph[airport]:
                next_dest = heapq.heappop(graph[airport])
                dfs(next_dest)
            route.append(airport)

        dfs("JFK")
        return route[::-1]
```

## Complexity

- **Time:** O(E log E), where E is the number of tickets (heap operations dominate)
- **Space:** O(E) for the adjacency map and recursion stack

## Real-World Uses

- Route reconstruction in logistics and delivery systems that must use every scheduled leg exactly once.
- DNA fragment reassembly, which is modeled as finding an Eulerian path through overlapping sequence reads.
- Circuit design tools that trace a single continuous path covering every connection exactly once.

## Key Takeaway

When a problem asks you to use every edge exactly once, think Eulerian path and Hierholzer's algorithm: recurse greedily, and build the answer in reverse post-order.
