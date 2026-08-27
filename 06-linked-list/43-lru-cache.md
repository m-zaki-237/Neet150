# 43. LRU Cache

## Problem

Design a Least Recently Used (LRU) cache with a fixed capacity. It should support `get(key)`, which returns the value for a key or `-1` if not present, and `put(key, value)`, which inserts or updates a value. Both operations must run in O(1) time. When the cache exceeds its capacity, it should evict the least recently used item.

## Example 1

### Input

```text
capacity = 2
put(1, 1)
put(2, 2)
get(1)       -> returns 1
put(3, 3)    -> evicts key 2 (least recently used)
get(2)       -> returns -1
```

### Expected Output

```text
1, -1
```

### Explanation

Getting key 1 made it recently used, so when the cache is full and key 3 is inserted, key 2 (untouched since) gets evicted instead.

## Example 2

### Input

```text
capacity = 1
put(1, 1)
put(2, 2)    -> evicts key 1 (capacity is only 1)
get(1)       -> returns -1
get(2)       -> returns 2
```

### Expected Output

```text
-1, 2
```

### Explanation

With capacity 1, inserting a second key immediately evicts the first.

## How to Solve

### Key Idea

Combine a hash map (for O(1) lookups) with a doubly linked list (for O(1) reordering) to track which items were used most and least recently.

### Approach

1. Keep a dictionary mapping `key -> node`, where each node lives in a doubly linked list ordered from least recently used (near the head) to most recently used (near the tail).
2. On `get`, if the key exists, move its node to the tail (mark as most recently used) and return its value; otherwise return -1.
3. On `put`, if the key exists, update its value and move it to the tail. If it's new, add a node at the tail; if this pushes the size over capacity, remove the node right after the dummy head (the least recently used one) and delete it from the dictionary too.

### Why It Works

The doubly linked list keeps items ordered by recency, and because it's doubly linked, any node can be unlinked and re-inserted at the tail in O(1) without scanning the list. The dictionary gives instant access to any node by key, so both operations avoid any linear search.

## Visual Explanation

Tracing Example 1 (`capacity = 2`) through the doubly linked list, ordered from least recently used (near head) to most recently used (near tail):

```mermaid
graph LR
    H1["head"] <--> K1a["1"] <--> K2a["2"] <--> T1["tail"]
```

After `put(1,1)`, `put(2,2)`: list is `[1, 2]`, with 1 least recently used.

```mermaid
graph LR
    H2["head"] <--> K2b["2"] <--> K1b["1"] <--> T2["tail"]
```

After `get(1)`: node 1 is unlinked and re-inserted at the tail, making 2 the new least recently used.

```mermaid
graph LR
    H3["head"] <--> K1c["1"] <--> K3c["3"] <--> T3["tail"]
```

After `put(3,3)`: the node right after `head` (key 2) is evicted, and 3 is inserted at the tail — matching `get(2) -> -1` in Example 1.

## Optimal Solution

```python
class Node:
    def __init__(self, key=0, val=0):
        self.key = key
        self.val = val
        self.prev = None
        self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.capacity = capacity
        self.cache = {}  # key -> Node

        # Dummy head and tail to avoid edge-case checks
        self.head = Node()
        self.tail = Node()
        self.head.next = self.tail
        self.tail.prev = self.head

    def _remove(self, node: Node) -> None:
        node.prev.next = node.next
        node.next.prev = node.prev

    def _insert_at_tail(self, node: Node) -> None:
        prev_last = self.tail.prev
        prev_last.next = node
        node.prev = prev_last
        node.next = self.tail
        self.tail.prev = node

    def get(self, key: int) -> int:
        if key not in self.cache:
            return -1
        node = self.cache[key]
        self._remove(node)
        self._insert_at_tail(node)
        return node.val

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])

        node = Node(key, value)
        self.cache[key] = node
        self._insert_at_tail(node)

        if len(self.cache) > self.capacity:
            lru = self.head.next
            self._remove(lru)
            del self.cache[lru.key]
```

## Complexity

- **Time:** O(1) for both `get` and `put`.
- **Space:** O(capacity) — the cache stores at most `capacity` nodes and dictionary entries.

## Real-World Uses

- Caching API responses or database query results with automatic eviction.
- Browser and CDN caching of recently accessed pages or assets.
- In-memory caches for expensive computations (e.g., image thumbnails, ML inference results).

## Key Takeaway

Pairing a hash map with a doubly linked list is the classic way to get O(1) access and O(1) reordering at the same time, which is exactly what an LRU cache needs.
