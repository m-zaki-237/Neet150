# 89. Graph Valid Tree

## Problem

You are given `n` nodes labeled `0` to `n - 1` and a list of undirected edges. Determine whether these edges form a valid tree, meaning the graph is fully connected and contains no cycles.

## Example 1

### Input

```text
n = 5, edges = [[0,1],[0,2],[0,3],[1,4]]
```

### Expected Output

```text
True
```

### Explanation

All 5 nodes are connected using exactly 4 edges and there is no cycle, so this is a valid tree.

## Example 2

### Input

```text
n = 5, edges = [[0,1],[1,2],[2,3],[1,3],[1,4]]
```

### Expected Output

```text
False
```

### Explanation

Nodes 1, 2, and 3 form a cycle (1-2, 2-3, 1-3), so this is not a valid tree.

## How to Solve

### Key Idea

A valid tree with `n` nodes must have exactly `n - 1` edges and must be fully connected with no cycles. Union-Find is a clean way to check both properties as you process edges.

### Approach

1. If the number of edges is not exactly `n - 1`, immediately return False (too many edges means a cycle; too few means disconnected).
2. Use Union-Find: for each edge, check if the two nodes are already in the same group. If they are, adding this edge creates a cycle, so return False.
3. Otherwise union the two nodes' groups. If you get through all edges without finding a cycle, and the edge count check passed, it's a valid tree.

### Why It Works

Exactly n-1 edges with no cycles guarantees full connectivity for n nodes (this is a known property of trees), and Union-Find efficiently detects a cycle whenever an edge tries to connect two nodes already in the same group.

## Visual Explanation

Union-Find trace for `edges = [[0,1],[0,2],[0,3],[1,4]]` (groups shown after each union):

```text
start:        {0} {1} {2} {3} {4}
union(0,1):   {0,1} {2} {3} {4}        (roots differ -> merge)
union(0,2):   {0,1,2} {3} {4}          (roots differ -> merge)
union(0,3):   {0,1,2,3} {4}            (roots differ -> merge)
union(1,4):   {0,1,2,3,4}              (roots differ -> merge)
```

No union ever finds a shared root, so no cycle is ever detected, and all 5 nodes end up in a single group after exactly `n - 1 = 4` edges — both conditions for a valid tree are satisfied.

## Optimal Solution

```python
class Solution:
    def validTree(self, n: int, edges: list[list[int]]) -> bool:
        if len(edges) != n - 1:
            return False

        parent = list(range(n))
        rank = [1] * n

        def find(x):
            while parent[x] != x:
                parent[x] = parent[parent[x]]  # path compression
                x = parent[x]
            return x

        def union(x, y):
            root_x, root_y = find(x), find(y)
            if root_x == root_y:
                return False  # cycle detected

            if rank[root_x] < rank[root_y]:
                root_x, root_y = root_y, root_x
            parent[root_y] = root_x
            rank[root_x] += rank[root_y]
            return True

        for a, b in edges:
            if not union(a, b):
                return False

        return True
```

## Complexity

- **Time:** O(n * α(n)), where α is the inverse Ackermann function (effectively constant)
- **Space:** O(n)

## Real-World Uses

- Validating that a network topology (e.g. computer network wiring) has no redundant loops.
- Checking organizational hierarchy charts for a single valid reporting structure without cycles.
- Verifying that a set of family/genealogy relationships forms a consistent tree.

## Key Takeaway

Union-Find is the natural tool for detecting cycles and tracking connectivity as edges are added one at a time; combined with the "n-1 edges" edge count check, it fully validates a tree.
