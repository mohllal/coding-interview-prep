# Pattern: In-Place Reversal of a Linked List

## Overview

The In-Place Reversal pattern manipulates linked list node pointers directly to reverse connections without allocating extra memory. Instead of creating new nodes or using auxiliary data structures, it rewires existing `next` pointers.

## Core idea

At each step, redirect the current node's `next` pointer to the previous node, then advance forward. This takes three pointers working in tandem — `previous`, `current`, and `next`. Save what's ahead, reverse the current link, then move forward.

Each iteration processes one node:

- Before we lose access to the rest of the list, we save `current.next`
- Then we can safely point `current.next` backward
- After processing, `previous` holds the last reversed node (new head when done)

### Example: reversing a full list

```plaintext
Initial:   1 ────▶ 2 ────▶ 3 ────▶ null
           ▲
           │
       current=1, previous=null

Step 1:    null ◀──── 1     2 ─────▶ 3 ─────▶ null
                      ▲     ▲
                      │     │
                  previous current

Step 2:    null ◀──── 1 ◀──── 2     3 ─────▶ null
                              ▲     ▲
                              │     │
                          previous current

Step 3:    null ◀──── 1 ◀──── 2 ◀──── 3      null
                                      ▲       ▲
                                      │       │
                                previous   current

Result:    3 ────▶ 2 ────▶ 1 ────▶ null (previous is new head)
```

The template:

```python
prev = None # previous node
curr = head # current node

while curr is not None:
    next = curr.next # save the next node
    curr.next = prev # reverse the pointer

    prev = curr # move previous forward
    curr = next # move current forward

return prev # return the new head
```

### Example: reversing a sub-list

When reversing only a segment, two anchors bracket the reversed portion:

```plaintext
[1,2,3,4,5,6], left=2, right=4  →  reverse nodes 2, 3, 4

  1 ──▶ [ 2 ──▶ 3 ──▶ 4 ] ──▶ 5 ──▶ 6 ──▶ null
  ▲       ▲           ▲        ▲
  │       │           │        │
prev_left left_node right_node after_right

          └── to be reversed ──┘
```

Two things make partial reversal work:

1. Start the reversal with `prev = after_right`: as each node gets flipped, its `next` pointer naturally chains toward `after_right`. The right end of the reversed segment connects to the rest of the list automatically — no explicit connection needed on the right.
2. One explicit connection on the left: after the loop, `prev` is the new head of the reversed segment. Set `prev_left.next = prev`.

```plaintext
  [1,2,3,4,5,6], left=2, right=4  →  reverse nodes 2, 3, 4

  start:   prev=5, curr=2

  1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ 6 ──▶ null

  ─────────────────────────────────────────────────────
  iter 1   2.next = 5   →   prev=2, curr=3

  1 ──▶ 2 ─────────────────────────┐
                                   ▼
        3 ──▶ 4 ─────────────────▶ 5 ──▶ 6 ──▶ null
        ▲
       curr

  ─────────────────────────────────────────────────────
  iter 2   3.next = 2   →   prev=3, curr=4

  1 ──▶ 2 ─────────────────────────┐
        ▲                          ▼
  3 ────┘         4 ─────────────▶ 5 ──▶ 6 ──▶ null
                  ▲
                 curr

  ─────────────────────────────────────────────────────
  iter 3   4.next = 3   →   prev=4, curr=5

  1 ───────────▶ 2 ──▶ 5 ──▶ 6 ──▶ null
                 ▲
  4 ──▶ 3 ───────┘
  ▲
 prev

  curr = 5 = after_right → loop ends

  ─────────────────────────────────────────────────────
  connect: 1.next = 4

  1 ──▶ 4 ──▶ 3 ──▶ 2 ──▶ 5 ──▶ 6 ──▶ null
```

The template:

```python
prev = after_right   # right anchor: starts the reversal
curr = left_node     # start of the reversed section

for _ in range(right - left + 1):
    next_node = curr.next
    curr.next = prev

    prev = curr
    curr = next_node

prev_left.next = prev  # connect left end to new head of reversed section
```

After reversing a group the original `left_node` becomes the group's tail. Save it as `prev_group_tail` to connect the next group's new head.

## Recognize it when

- The problem asks to reverse all or part of a linked list in-place
- Reordering nodes without extra space (palindrome check, merge-sort halves)
- "Reverse from position `m` to `n`" or "reverse every `k` nodes"
- The result is a rearranged version of the same nodes — links change, values do not

Common shapes to look out for:

- "Reverse from position m to n"
- "Reverse every k nodes"
- "Rotate list by k positions" (rotation = cut + rewire)

## Reach for something else when

- You only need to reverse the *values*, not the structure: copy to an array, reverse, copy back
- The list is doubly-linked: in a singly-linked list, each node only has a `next` pointer going forward and reversing means redirecting those pointers to go backward instead, which is what this pattern does. A doubly-linked list already has a `prev` pointer on every node, so you can traverse in either direction without changing any pointers — just follow `prev` from the tail instead of `next` from the head
- The reordering is arbitrary rather than a structured reversal: a sort or different algorithm fits better

## Pitfalls

- Forgetting to save `next` before flipping: `curr.next = prev` destroys the only forward reference. Always `next_node = curr.next` first.
- Wrong connection order on partial reversals: set `prev_left.next = prev` *after* the reversal loop. Connecting early breaks the segment's forward chain before it is reversed.
- Missing the `left == 1` edge case: when the reversed segment starts at the head, there is no `prev_left`. Return `prev` directly as the new head.
- Not reducing `k` modulo length on rotation: `k` can exceed the list length. `k % length` is the effective rotation; skipping this walks the whole list needlessly and gives wrong results when `k` is a multiple of `length`.

## Key Takeaways

- Three pointers — `prev`, `curr`, `next_node` — are enough to reverse any linked list in $O(1)$ space.
- Start partial reversals with `prev = after_right`: the right end of the reversed segment auto-connects to the rest of the list.
- After reversing a group, its original head is now its tail: save it as `prev_group_tail` to connect the next reversed group.
- Rotation is a cut-and-rewire: find position `length - k`, break there, and point the old tail at the old head.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
