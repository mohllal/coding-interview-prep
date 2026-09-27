---
title: Implementing a Stack
difficulty: Medium
leetcode_title: Min Stack
leetcode: https://leetcode.com/problems/min-stack/
tags:
  - Stack
  - Design
---

# Implementing a Stack

## Problem description

Design a stack that supports `push`, `pop`, `top`, and retrieving the minimum element — all in $O(1)$ time.

Implement the `MinStack` class:

- `push(val)` — pushes `val` onto the stack
- `pop()` — removes the top element
- `top()` — returns the top element without removing it
- `getMin()` — returns the minimum element in the stack

## Examples

**Example 1:**

```plaintext
Input:
  push(-2), push(0), push(-3)
  getMin() → -3
  pop()
  top()    → 0
  getMin() → -2
```

## Constraints

- `-2^31 <= val <= 2^31 - 1`
- `pop`, `top`, and `getMin` are always called on a non-empty stack
- At most `3 * 10^4` calls in total

## Hints

<details>
<summary>Hint 1</summary>

`getMin` must be $O(1)$, so you cannot scan the stack. The minimum needs to be instantly available. Where could you store it so it is always at hand?

</details>

<details>
<summary>Hint 2</summary>

The global minimum changes only when you push a new minimum or pop the current minimum. What if each stack entry remembered the minimum at the time it was pushed?

</details>

## Solution

### Intuition

Every element is paired with the minimum of the entire stack *at the moment it was pushed*. When that element is at the top, `getMin` returns its stored minimum. When the element is popped, the previous element's stored minimum becomes current again — automatically restoring the correct answer with no scanning.

```plaintext
push(-2)  stack: [(-2, min=-2)]              getMin = -2
push( 0)  stack: [(-2, -2), (0, min=-2)]     getMin = -2
push(-3)  stack: [(-2, -2), (0, -2), (-3, min=-3)]  getMin = -3
pop()     stack: [(-2, -2), (0, min=-2)]     getMin = -2
top()  →  0
```

### Algorithm

1. Each `push` computes the new minimum: `min(val, current_min)` and stores `(val, new_min)` as a pair
2. `pop`, `top`, and `getMin` read from the top pair

### Complexity analysis

- Time complexity: $O(1)$ — all four operations
- Space complexity: $O(n)$ — the paired stack

```python
class MinStack:
    def __init__(self):
        self._stack = []  # each entry: [value, min_so_far]

    def push(self, val: int) -> None:
        previous_min = self._stack[-1][1] if self._stack else float("inf")
        current_min = min(val, previous_min)
        self._stack.append([val, current_min])

    def pop(self) -> None:
        self._stack.pop()

    def top(self) -> int:
        return self._stack[-1][0]

    def getMin(self) -> int:
        return self._stack[-1][1]
```
