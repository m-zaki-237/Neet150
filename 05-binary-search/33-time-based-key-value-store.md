# 33. Time Based Key-Value Store

## Problem

Design a key-value store where each key can have multiple values, each tagged with a timestamp. You need to support `set(key, value, timestamp)` to store a value at a given timestamp, and `get(key, timestamp)` to return the value that was set at the largest timestamp less than or equal to the given timestamp. Timestamps passed to `set` are always increasing for the same key. If no such value exists, `get` returns an empty string.

## Example 1

### Input

```text
set("foo", "bar", 1)
get("foo", 1)
get("foo", 3)
```

### Expected Output

```text
None
"bar"
"bar"
```

### Explanation

set() returns nothing. get("foo", 1) finds the value set exactly at time 1. get("foo", 3) still returns "bar" because no later value exists, and 1 is the closest timestamp <= 3.

## Example 2

### Input

```text
set("foo", "bar", 1)
set("foo", "bar2", 4)
get("foo", 4)
get("foo", 5)
get("foo", 0)
```

### Expected Output

```text
None
None
"bar2"
"bar2"
""
```

### Explanation

At timestamp 4, "bar2" overwrites the previous value going forward. get("foo", 5) still returns "bar2" since it's the latest value at or before time 5. get("foo", 0) returns "" because no value was set at or before time 0.

## How to Solve

### Key Idea

Store each key's values as a list of (timestamp, value) pairs in the order they are set. Since timestamps only increase, this list is always sorted by timestamp, so you can binary search it to find the most recent entry at or before the requested timestamp.

### Approach

1. Use a dictionary mapping each key to a list of `(timestamp, value)` pairs. On `set`, just append to that key's list (it stays sorted since timestamps increase).
2. On `get`, look up the key's list; if it doesn't exist, return `""`.
3. Binary search the list for the rightmost timestamp that is less than or equal to the target timestamp, and return its value, or `""` if none qualifies.

### Why It Works

Because entries are always appended in increasing timestamp order, the list is naturally sorted, which lets binary search find the "latest timestamp <= target" in logarithmic time instead of scanning every entry.

## Visual Explanation

```text
entries for "foo": [(1,"bar"), (4,"bar2"), (7,"bar3")]
get("foo", 5)
 L        M         R    -> ts=4 <= 5, record result="bar2", search right (left=mid+1)
          LR              -> ts=7 > 5, search left (right=mid-1)
 result stays "bar2" -> return "bar2"
```

## Optimal Solution

```python
from collections import defaultdict


class TimeMap:
    def __init__(self):
        self.store: dict[str, list[tuple[int, str]]] = defaultdict(list)

    def set(self, key: str, value: str, timestamp: int) -> None:
        self.store[key].append((timestamp, value))

    def get(self, key: str, timestamp: int) -> str:
        entries = self.store.get(key)
        if not entries:
            return ""

        left, right = 0, len(entries) - 1
        result = ""

        while left <= right:
            mid = (left + right) // 2
            if entries[mid][0] <= timestamp:
                result = entries[mid][1]
                left = mid + 1
            else:
                right = mid - 1

        return result
```

## Complexity

- **Time:** O(log n) per `get`, O(1) per `set`, where n is the number of entries stored for a key
- **Space:** O(n) total, for storing all set entries

## Real-World Uses

- Versioned data lookups, such as fetching a config or document as of a given point in time.
- Time-travel queries in databases (finding the row state at a past snapshot/timestamp).
- Cache/CDN systems resolving the most recent valid entry at or before a request timestamp.

## Key Takeaway

When data arrives in sorted order over time (like increasing timestamps), you can store it in a simple list and use binary search for fast "closest value at or before X" lookups instead of a more complex structure.
