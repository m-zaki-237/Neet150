# 139. Happy Number

## Problem

A "happy number" is found by repeatedly replacing the number with the sum of the squares of its digits. If this process eventually reaches 1, the number is happy. If it loops forever in a cycle that never reaches 1, it is not happy. Given an integer `n`, return `True` if it is happy, else `False`.

## Example 1

### Input

```text
n = 19
```

### Expected Output

```text
True
```

### Explanation

19 -> 1^2+9^2=82 -> 8^2+2^2=68 -> 6^2+8^2=100 -> 1^2+0^2+0^2=1. It reaches 1, so it is happy.

## Example 2

### Input

```text
n = 2
```

### Expected Output

```text
False
```

### Explanation

2 -> 4 -> 16 -> 37 -> 58 -> 89 -> 145 -> 42 -> 20 -> 4 ... this enters a repeating cycle that never reaches 1.

## How to Solve

### Key Idea

This is really a cycle-detection problem: repeatedly apply the "sum of squared digits" function, and detect whether you loop back to a previously seen value (never reaching 1) using either a set of seen numbers or the fast/slow pointer technique.

### Approach

1. Write a helper function that takes a number and returns the sum of the squares of its digits.
2. Use two pointers, `slow` and `fast`, both starting at `n`. Move `slow` one step (apply the function once) and `fast` two steps (apply the function twice) each iteration.
3. If `fast` ever becomes 1, the number is happy — return True.
4. If `slow == fast` at any point before reaching 1, a cycle exists that doesn't include 1 — return False.

### Why It Works

This is the same idea as detecting a cycle in a linked list. Since the sequence of values is deterministic (each number maps to exactly one next number), if it doesn't reach 1 it must eventually repeat a value, forming a cycle. The fast/slow pointers are guaranteed to meet inside that cycle.

## Visual Explanation

```text
start:    slow = 19                 fast = next(19) = 82

step 1:   slow = next(19) = 82      fast = next(next(82)) = next(68) = 100

step 2:   slow = next(82) = 68      fast = next(next(100)) = next(1) = 1   <- fast == 1, stop: Happy!
```

## Optimal Solution

```python
class Solution:
    def isHappy(self, n: int) -> bool:
        def next_number(x: int) -> int:
            total = 0
            while x > 0:
                digit = x % 10
                total += digit * digit
                x //= 10
            return total

        slow, fast = n, next_number(n)
        while fast != 1 and slow != fast:
            slow = next_number(slow)
            fast = next_number(next_number(fast))

        return fast == 1
```

## Complexity

- **Time:** O(log n) per function call, and the cycle length is bounded, so overall it is effectively O(log n)
- **Space:** O(1) extra space (fast/slow pointers avoid needing a hash set)

## Real-World Uses

- Cycle detection in linked lists and iterative algorithms (Floyd's tortoise-and-hare pattern reused elsewhere).
- Detecting infinite loops in state machines or workflow engines that repeatedly transform state.
- Validating convergence of iterative numerical processes before they run forever.

## Key Takeaway

Whenever a problem involves repeatedly applying a deterministic function and asking "does this reach a target or loop forever," think cycle detection — either a hash set of seen states or the O(1)-space fast/slow pointer trick.
