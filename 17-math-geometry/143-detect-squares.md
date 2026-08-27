# 143. Detect Squares

## Problem

Design a data structure that supports adding 2D points (with possibly duplicate points) and, given a query point, counts how many axis-aligned squares can be formed using that query point and three other points already added to the structure. A valid square must have its sides parallel to the x and y axes.

## Example 1

### Input

```text
Operations:
add([3,10])
add([11,2])
add([3,2])
count([11,10])
```

### Expected Output

```text
1
```

### Explanation

The points (3,10), (11,2), (3,2), and the query (11,10) form one axis-aligned square of side length 8.

## Example 2

### Input

```text
Operations:
add([1,1])
add([1,1])
add([3,3])
add([3,1])
count([1,3])
```

### Expected Output

```text
2
```

### Explanation

The query (1,3) can form a square with (1,1), (3,1), and (3,3). Since (1,1) was added twice, this square can be counted twice (once for each duplicate copy of (1,1)).

## How to Solve

### Key Idea

For a query point `(x, y)`, any axis-aligned square must have another point sharing the same x-coordinate (a point directly above or below) and another point sharing the same y-coordinate (a point directly left or right), with side length equal to the distance between them. Use a frequency count of points to quickly check how many times the required corner points exist.

### Approach

1. Store all added points in a frequency map (dictionary counting how many times each `(x, y)` point was added), plus keep a list of points for iterating.
2. For `count(x, y)`: iterate over every stored point `(px, py)` that shares the same y-coordinate as the query and where `px != x` (this point acts as one diagonal-adjacent corner, since the square side length is `abs(px - x)`).
3. For each such point, the side length `d = px - x`. The other two corners needed are `(x, y + d)` and `(px, y + d)`. Look up how many times each of these appears in the frequency map.
4. Multiply the counts of `(px, py)`, `(x, y + d)`, and `(px, y + d)` together and add to the running total (this accounts for duplicate points forming multiple distinct squares).

### Why It Works

An axis-aligned square is fully determined once you fix the query point and one other point that shares a row (same y). The side length is the horizontal distance between them, which tells you exactly where the other two corners must be. Counting how many stored points match each of those exact corner positions (respecting duplicates) gives the exact number of squares that can be formed.

## Visual Explanation

```text
query = (11,10)             stored point (3,10) shares y=10, so it's a match: px=3

d = px - x = 3 - 11 = -8

corner1 = (x, y+d) = (11, 2)      corner2 = (px, y+d) = (3, 2)

(11,2) --------------- (3,2)          <- both are stored points
   |                      |
   |                      |
(11,10) -------------- (3,10)
  query                 match (same y)

count += point_count[(3,10)] * point_count[(11,2)] * point_count[(3,2)]
       = 1 * 1 * 1 = 1
```

## Optimal Solution

```python
from collections import defaultdict, Counter

class DetectSquares:
    def __init__(self):
        self.point_count = Counter()
        self.points = []

    def add(self, point: list[int]) -> None:
        x, y = point
        self.point_count[(x, y)] += 1
        self.points.append((x, y))

    def count(self, point: list[int]) -> int:
        x, y = point
        total = 0

        for px, py in list(self.point_count.keys()):
            if py != y or px == x:
                continue

            d = px - x
            # The two other corners needed to complete the square
            corner1 = (x, y + d)
            corner2 = (px, y + d)

            total += (
                self.point_count[(px, py)]
                * self.point_count.get(corner1, 0)
                * self.point_count.get(corner2, 0)
            )

        return total
```

## Complexity

- **Time:** O(1) amortized for `add`; O(n) for `count`, where n is the number of distinct stored points (since it scans points sharing the query's y-coordinate)
- **Space:** O(n) to store all added points and their counts

## Real-World Uses

- Computational geometry libraries detecting axis-aligned shapes among point sets.
- Collision detection systems in games checking for square/rectangular alignments between objects.
- Computer vision tasks that detect grid or checkerboard patterns from feature points.

## Key Takeaway

When a geometric shape is constrained to be axis-aligned, you can derive the exact coordinates of the remaining corners from just one known pair of points, turning a search problem into fast dictionary lookups.
