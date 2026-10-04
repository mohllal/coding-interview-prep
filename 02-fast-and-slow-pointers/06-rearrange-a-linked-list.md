---
title: Rearrange a Linked List
difficulty: Medium
leetcode_title: Reorder List
leetcode: https://leetcode.com/problems/reorder-list/
tags:
  - Linked List
  - Two Pointers
  - Stack
  - Recursion
---

# Rearrange a Linked List

## Problem description

Given the head of a singly linked list, reorder it to: `L0 → Ln → L1 → Ln-1 → L2 → Ln-2 → ...`

You may not modify the values in the list's nodes. Only nodes themselves may be changed.

## Examples

**Example 1:**

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ null

becomes

1 ──▶ 4 ──▶ 2 ──▶ 3 ──▶ null

Input: head = [1,2,3,4]
Output: [1,4,2,3]
```

**Example 2:**

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null

becomes

1 ──▶ 5 ──▶ 2 ──▶ 4 ──▶ 3 ──▶ null

Input: head = [1,2,3,4,5]
Output: [1,5,2,4,3]
```

## Constraints

- The number of nodes in the list is in the range `[1, 5 * 10^4]`.
- `1 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

The target order alternates between the front of the list and the back. Walking backwards is exactly what a singly linked list cannot do.

</details>

<details>
<summary>Hint 2</summary>

So make it possible: split at the middle, reverse the second half, then weave the two halves together one node at a time.

</details>

## Solution

### Intuition

This problem combines three linked list operations:

1. **Find the middle** - Split the list into two halves
2. **Reverse the second half** - So we can interleave from both ends
3. **Merge alternately** - Weave nodes from first half and reversed second half

This is similar to [Palindrome Linked List](./05-palindrome-linked-list.md) but instead of comparing, we're merging.

Odd and even lengths need no special handling: the reversal starts at the middle node, so both halves end on that **same** node.

**Odd length:**

```plaintext
1. Find the middle — slow stops on the center node

   1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null
               ▲
             middle

2. Reverse from the middle onward — the middle node is shared by both halves

   head                     reversed head
    │                             │
    ▼                             ▼
    1 ──▶ 2 ──▶ 3 ◀── 4 ◀──────── 5
                │
                ▼
               null

3. Weave: front walks in from the head, back walks in from the reversed head

   step   front   back   relink        list so far
   1        1       5    1 → 5 → 2     1 → 5 → 2
   2        2       4    2 → 4 → 3     1 → 5 → 2 → 4 → 3
   stop     3       3    back is on the shared middle — already the tail

   1 ──▶ 5 ──▶ 2 ──▶ 4 ──▶ 3 ──▶ null
```

**Even length:**

```plaintext
1. Find the middle — slow stops on the second of the two middle nodes

   1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ null
               ▲
             middle

2. Reverse from the middle onward — the middle node is shared by both halves

   head               reversed head
    │                       │
    ▼                       ▼
    1 ──▶ 2 ──▶ 3 ◀──────── 4
                │
                ▼
               null

3. Weave: front walks in from the head, back walks in from the reversed head

   step   front   back   relink        list so far
   1        1       4    1 → 4 → 2     1 → 4 → 2
   stop     2       3    back is on the shared middle — 2 → 3 is already in place

   1 ──▶ 4 ──▶ 2 ──▶ 3 ──▶ null
```

### Algorithm

1. Find the middle node using slow/fast pointers.
2. Reverse the list from the middle onward.
3. Weave the halves: walk `front` from the head and `back` from the reversed head, linking `front → back → front.next` each step.
4. Stop when `back` reaches the shared middle node (`back.next is None`).

### Complexity analysis

- Time complexity: $O(n)$ - Three linear passes (find middle, reverse, merge)
- Space complexity: $O(1)$ - Only pointer manipulations, no extra storage

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def get_middle_node(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

        return slow

    def reverse_list(self, head: Optional[ListNode]) -> Optional[ListNode]:
        previous_node = None
        current_node = head

        while current_node is not None:
            next_node = current_node.next
            current_node.next = previous_node

            previous_node = current_node
            current_node = next_node

        return previous_node

    def reorder_list(self, head: Optional[ListNode]) -> None:
        if head is None or head.next is None:
            return

        middle = self.get_middle_node(head)
        reversed_head = self.reverse_list(middle)

        front = head
        back = reversed_head

        # stop on the shared middle node: it's already in place as the tail
        while back.next is not None:
            front_next = front.next
            back_next = back.next

            front.next = back
            back.next = front_next

            front = front_next
            back = back_next
```

Why `back.next is not None` and not `back is not None`? The shared middle node is already in its final place as the tail. Running one more step would try to weave it in after itself. On an even length that sets `middle.next = middle`, a cycle:

```plaintext
1 ──▶ 4 ──▶ 2 ──▶ 3 ──┐      with `while back is not None`
                  ▲   │
                  └───┘
```

`front` never needs a check of its own: the first half is always at least as long as the reversed half, so `back` runs out first.
