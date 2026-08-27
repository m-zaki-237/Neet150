# 98. Cheapest Flights Within K Stops

## Problem

There are `n` cities connected by flights, given as `[from, to, price]`. Starting at city `src`, find the cheapest price to reach city `dst` using at most `k` stops (intermediate cities) along the way. If no such route exists, return -1.

## Example 1

### Input

```text
n = 4, flights = [[0,1,100],[1,2,100],[2,3,100],[0,2,500]], src = 0, dst = 3, k = 1
```

### Expected Output

```text
600
```

### Explanation

With at most 1 stop, the route 0 -> 1 -> 2 -> 3 needs 2 stops, which isn't allowed. The best route within 1 stop is 0 -> 2 -> 3, costing 500 + 100 = 600.

## Example 2

### Input

```text
n = 3, flights = [[0,1,100],[1,2,100],[0,2,500]], src = 0, dst = 2, k = 0
```

### Expected Output

```text
500
```

### Explanation

With 0 stops allowed, only the direct flight 0 -> 2 is usable, costing 500 (the two-hop route would need 1 stop, which isn't allowed).

## How to Solve

### Key Idea

This is a shortest path problem with a limited number of edges (at most k+1 flights), so plain Dijkstra doesn't directly apply since cheaper paths with more stops shouldn't block valid ones with fewer stops. A Bellman-Ford style relaxation, limited to k+1 rounds, works cleanly.

### Approach

1. Initialize an array of costs to reach every city, starting as infinity except `src` which is 0.
2. Repeat the relaxation step exactly `k + 1` times (allowing up to k+1 edges total): for each flight `(u, v, price)`, if `cost[u] + price` is cheaper than the current best cost to reach `v` (using the previous round's costs), update it.
3. Use a copy of the cost array each round so updates from the same round don't chain together and exceed the stop limit.
4. After all rounds, return `cost[dst]` if it's still finite, otherwise -1.

### Why It Works

Bellman-Ford relaxes all edges repeatedly, and each round represents allowing one more edge (flight) in the path. By capping it at `k + 1` rounds and using a fresh snapshot of costs each round, we guarantee we never build a path using more than `k` intermediate stops, while still finding the cheapest one within that limit.

## Visual Explanation

```text
Bellman-Ford, capped at k + 1 = 2 relaxation rounds (costs = [city0, city1, city2, city3]):

  start:    costs = [0, inf, inf, inf]

  round 1 (using round-0 snapshot for every edge):
    0->1 (100): 0+100=100   -> city1 = 100
    0->2 (500): 0+500=500   -> city2 = 500
    (1->2 and 2->3 skipped: city1/city2 still inf in the snapshot)
    costs = [0, 100, 500, inf]

  round 2 (using round-1 snapshot for every edge):
    1->2 (100): 100+100=200 -> city2 improves to 200 (too late to help dst here)
    2->3 (100): 500+100=600 -> city3 = 600   (uses round-1's city2=500, not the 200 just computed)
    costs = [0, 100, 200, 600]

  answer = costs[dst=3] = 600
```

## Optimal Solution

```python
class Solution:
    def findCheapestPrice(self, n: int, flights: list[list[int]], src: int, dst: int, k: int) -> int:
        INF = float("inf")
        costs = [INF] * n
        costs[src] = 0

        for _ in range(k + 1):
            new_costs = costs.copy()
            for u, v, price in flights:
                if costs[u] != INF and costs[u] + price < new_costs[v]:
                    new_costs[v] = costs[u] + price
            costs = new_costs

        return costs[dst] if costs[dst] != INF else -1
```

## Complexity

- **Time:** O(k * E), where E is the number of flights
- **Space:** O(n) for the cost arrays

## Real-World Uses

- Travel-booking systems finding the cheapest flight itinerary within a maximum number of layovers.
- Network routing with a hop-count limit, balancing cost against latency introduced by extra hops.
- Financial arbitrage or currency-conversion systems bounding the number of intermediate trades allowed.

## Key Takeaway

When a shortest-path problem has a hard limit on the number of edges used, think Bellman-Ford with a bounded number of relaxation rounds instead of plain Dijkstra, and always relax using the previous round's snapshot to respect the limit.
