# Pattern: Monotonic Stack

## Overview

A monotonic stack is an ordinary stack that is kept in sorted order — every push pops anything that would violate the ordering. The key insight is that popping is not waste: it is work. Each pop reveals information (the boundary, the previous element, the answer for what got popped) that you could not see cheaply any other way.

> Core idea: park elements on a stack in sorted order, and resolve them the moment something breaks the sort.

Because each element is pushed exactly once and popped at most once, the total work is $O(n)$ even when the inner pop-loop fires on every iteration.

## The core pattern

```plaintext
arr = [2, 1, 5, 3, 4]   — find the next greater element for each index

i=0 (2): stack empty, push.                    stack: [0]
i=1 (1): 1 ≤ 2, push.                          stack: [0, 1]
i=2 (5): 5 > 1 → pop 1, answer[1] = 5
         5 > 2 → pop 0, answer[0] = 5
         push.                                  stack: [2]
i=3 (3): 3 ≤ 5, push.                          stack: [2, 3]
i=4 (4): 4 > 3 → pop 3, answer[3] = 4
         4 ≤ 5, push.                           stack: [2, 4]

Remaining (no next greater): answer[2] = -1, answer[4] = -1
```

The stack holds *indices* (not values) so that after a pop you can both read the value (`arr[j]`) and locate the boundaries (`stack[-1]` after the pop, and the current index `i`).

## Templates

**Monotonic decreasing stack** — the stack stays largest-to-smallest (top is the smallest so far). An element is pushed only after popping everything smaller than it. Use when you need the *next greater* element, or when later larger values "dominate" earlier smaller ones.

```plaintext
stack ← empty

for each element x in arr:
    while stack is not empty and x > top of stack:
        popped ← pop stack
        # popped's right boundary is x
        # popped's left boundary is the new top (or sentinel -1)
        process(popped)
    push x onto stack

# anything remaining on the stack has no greater element to its right
while stack is not empty:
    process(pop stack)
```

**Monotonic increasing stack** — the stack stays smallest-to-largest (top is the largest so far). An element is pushed only after popping everything larger than it. Use when you need the *next smaller* element, or when later smaller values dominate earlier larger ones.

```plaintext
stack ← empty

for each element x in arr:
    while stack is not empty and x < top of stack:
        popped ← pop stack
        # popped's right boundary is x
        # popped's left boundary is the new top (or sentinel -1)
        process(popped)
    push x onto stack

# anything remaining on the stack has no smaller element to its right
while stack is not empty:
    process(pop stack)
```

**Three knobs** tune the exact question:

| Change                          | Effect                                                                               |
|---------------------------------|--------------------------------------------------------------------------------------|
| `>` → `>=` or `<` → `<=`        | equal elements resolve each other (affects double-counting in contribution problems) |
| iterate right-to-left           | find the *previous* boundary instead of the *next* one                               |
| store indices instead of values | lets you compute distances and access both boundaries after a pop                    |

## Variants

### 1. Next greater / smaller element

The baseline: for each element find the nearest element to the right that is larger (or smaller). One stack scan, one result array, $O(n)$.

### 2. Stack as a running result buffer

The stack is not a side-channel — it *is* the answer being built. Push characters or values; pop when the top violates some condition (a match, a run length reaching `k`). Drain the stack at the end to produce the result string or list.

```python
stack = []
for ch in s:
    if stack and stack[-1] == ch:
        stack.pop()        # adjacent duplicate — cancel it
    else:
        stack.append(ch)
return ''.join(stack)
```

### 3. Contribution counting

Instead of answering "what is the next greater element?", ask "how many subarrays is element `j` the minimum of?" When `arr[i]` pops `arr[j]`, you know `j`'s right boundary (`i`) and left boundary (`stack[-1]` after the pop). Count the subarrays algebraically:

```python
left_count  = j - left   # subarrays can start anywhere in (left, j]
right_count = right - j  # subarrays can end anywhere in [j, right)
contribution = arr[j] * left_count * right_count
```

Use strict vs. non-strict comparisons on the two sides to avoid double-counting equal elements.

### 4. Greedy construction

Build the lexicographically smallest (or largest) result by popping elements that should not appear before the current one. The stack is the result; the remaining budget (e.g. `k` removals left) is the termination condition.

```python
stack = []
for digit in num:
    while k and stack and stack[-1] > digit:
        stack.pop()
        k -= 1
    stack.append(digit)
```

## Recognize it when

- You are scanning left-to-right and an element "answers" or "invalidates" earlier elements — the pop gives you the answer for what was parked.
- You need the *nearest* greater or smaller element in $O(n)$ (brute force is $O(n^2)$).
- The problem asks for a sum or count over all subarrays, and you suspect each element's contribution can be computed from its expansion boundaries.
- You are building a result string or sequence by cancelling adjacent items — the stack *is* the result.
- You need to greedily remove elements to minimise or maximise a sequence — each removal decision uses the current element vs. the stack top.

## Reach for something else when

- You need the maximum or minimum *inside* a sliding window — a monotonic deque (double-ended queue) handles additions and removals from both ends, which a stack cannot.
- You need the globally best parked element, not the most recently parked — that is a heap.
- Elements are answered by the first *oldest* (not newest) parked item — that is a queue, not a stack.

## Pitfalls

- After the main scan, elements still on the stack have no right boundary. Omitting the drain loop silently drops their contribution or leaves their result unset.
- Storing values is fine for simple next-greater queries, but the moment you need a distance (`i - j`), a left boundary (`stack[-1]` after the pop), or to write into a result array, you need the index. Default to storing indices.
- `>` and `<` mean equal elements park on top of each other and are resolved later; `>=` and `<=` mean they resolve each other immediately. In contribution-counting problems this changes which side of a boundary owns equal elements — getting it wrong causes double-counting.
- When building the smallest number by removing digits, the result stack may start with zeros. Strip them before returning, and guard against an empty result.
- In stack-as-buffer problems the bottom is the front of the output. Joining left-to-right (`''.join(stack)`) gives the right order; reversing first gives the wrong one.

## Key takeaways

- Popping is not overhead — it is work. Each pop answers a parked element in $O(1)$, giving the whole scan $O(n)$ despite the nested loop.
- Store indices, not values. You almost always need both the value and the position after a pop.
- The drain loop is half the algorithm. Skipping it loses every element that never found its boundary.
- Strict comparison on one side, non-strict on the other is the standard trick for avoiding double-counting when equal elements exist.
- Monotonic stack finds boundaries; monotonic deque finds window extremes. They look similar but answer different questions.

## Complexity

Every element is pushed once and popped at most once. The total number of stack operations across the whole scan is $O(n)$, even when the inner `while` loop fires many times on a single iteration. Space is $O(n)$ for the stack.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
