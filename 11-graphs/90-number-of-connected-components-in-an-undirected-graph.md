# 90. Number of Connected Components in an Undirected Graph

## Problem

You are given `n` nodes labeled `0` to `n - 1` and a list of undirected edges. Return the number of separate connected components (groups of nodes that are connected to each other, directly or indirectly).

## Example 1

### Input

```text
n = 5, edges = [[0,1],[1,2],[3,4]]
```

### Expected Output

```text
2
```

### Explanation

Nodes 0, 1, 2 form one connected group, and nodes 3, 4 form another.

## Example 2

### Input

```text
n = 5, edges = [[0,1],[1,2],[2,3],[3,4]]
```

### Expected Output

```text
1
```

### Explanation

All 5 nodes are connected in a single chain, forming one component.

## How to Solve

### Key Idea

Use Union-Find to merge nodes that share an edge into the same group. At the end, the number of distinct groups is the answer.

### Approach

1. Initialize each node as its own parent (n separate groups to start).
2. For each edge, union the two nodes together, reducing the total group count by one if they weren't already connected.
3. Return the final count of remaining groups (or count distinct roots at the end).

### Why It Works

Union-Find directly models "these nodes belong to the same group", and every successful union merges two previously separate groups into one, so tracking merges gives the final component count.

## Visual Explanation

Union-Find trace for `edges = [[0,1],[1,2],[3,4]]` (n = 5, so components starts at 5):

```text
start:        {0} {1} {2} {3} {4}      components = 5
union(0,1):   {0,1} {2} {3} {4}        components = 4
union(1,2):   {0,1,2} {3} {4}          components = 3
union(3,4):   {0,1,2} {3,4}            components = 2
```

Each union merges two previously separate groups (a real decrease), so `components` drops by exactly 1 each time, ending at 2 — matching the two groups `{0,1,2}` and `{3,4}`.

## Optimal Solution

```python
class Solution:
    def countComponents(self, n: int, edges: list[list[int]]) -> int:
        parent = list(range(n))
        rank = [1] * n
        components = n

        def find(x):
            while parent[x] != x:
                parent[x] = parent[parent[x]]
                x = parent[x]
            return x

        def union(x, y):
            nonlocal components
            root_x, root_y = find(x), find(y)
            if root_x == root_y:
                return

            if rank[root_x] < rank[root_y]:
                root_x, root_y = root_y, root_x
            parent[root_y] = root_x
            rank[root_x] += rank[root_y]
            components -= 1

        for a, b in edges:
            union(a, b)

        return components
```

## Complexity

- **Time:** O(n + E * α(n)), where E is the number of edges
- **Space:** O(n)

## Real-World Uses

- Counting isolated clusters of users or friend groups in a social network.
- Identifying separate subnetworks in a computer network topology.
- Finding distinct clusters of related data points after grouping by similarity edges.

## Key Takeaway

Union-Find is ideal for counting connected components: start with every node separate and merge as edges are processed, tracking the group count directly.
