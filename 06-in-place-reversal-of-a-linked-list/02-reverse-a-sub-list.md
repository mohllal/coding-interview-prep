---
title: Reverse a Sub-List
difficulty: Medium
leetcode_title: Reverse Linked List II
leetcode: https://leetcode.com/problems/reverse-linked-list-ii/
tags:
  - Linked List
---

# Reverse a Sub-List

## Problem description

Given the `head` of a singly linked list and two integers `left` and `right` where `left <= right`, reverse the nodes of the list from position `left` to position `right`, and return the reversed list.

## Examples

**Example 1:**

```plaintext
Input: head = [1,2,3,4,5], left = 2, right = 4
Output: [1,4,3,2,5]
```

**Example 2:**

```plaintext
Input: head = [5], left = 1, right = 1
Output: [5]
```

## Constraints

- The number of nodes in the list is `n`.
- `1 <= n <= 500`
- `-500 <= Node.val <= 500`
- `1 <= left <= right <= n`

## Hints

<details>
<summary>Hint 1</summary>

The reversal itself is the standard loop. The real work is stitching the reversed piece back between the nodes on either side.

</details>

<details>
<summary>Hint 2</summary>

Before reversing, hold on to the node just before position `p` and the node that starts the sublist — after reversing, the second becomes the sublist's tail.

</details>

## Solution

### Intuition

This extends the basic reversal by only reversing a portion of the list. We need to:

1. Find the boundaries of the segment to reverse
2. Reverse just that segment
3. Reconnect it with the unchanged parts

Seeding `prev = after_right` before reversing means the last node of the sublist naturally ends up pointing at `after_right` when the loop finishes — the right end connects itself. Only the left end needs an explicit stitch.

```plaintext
[1,2,3,4,5], left=2, right=4

Phase 1 — locate boundaries:

  1 ──▶ [ 2 ──▶ 3 ──▶ 4 ] ──▶ 5 ──▶ null
  ▲       ▲           ▲        ▲
  │       │           │        │
prev_left left_node right_node after_right

Phase 2 — reverse [2,3,4], seeding prev = after_right = 5:

  prev=5, curr=2

  iter 1   2.next = 5     prev=2, curr=3
  iter 2   3.next = 2     prev=3, curr=4
  iter 3   4.next = 3     prev=4, curr=5

  reversed segment: 4 ──▶ 3 ──▶ 2 ──▶ 5   (right end already connected)
                    ▲
                   prev

Phase 3 — connect left: prev_left (1) .next = prev (4):

  1 ──▶ 4 ──▶ 3 ──▶ 2 ──▶ 5 ──▶ null
```

### Algorithm

1. Traverse to find `left_node` and `prev_left` (node before position `left`)
2. Traverse to find `right_node` and `after_right` (node after position `right`)
3. Reverse the segment: start with `prev = after_right`, iterate `right - left + 1` times
4. Reconnect: point `prev_left.next` to `prev` (new head of reversed segment)
5. Handle edge case: if `left == 1`, return `prev` as the new head

### Complexity analysis

- Time complexity: $O(n)$ - at most two passes through the list
- Space complexity: $O(1)$ - only using a few pointers

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverse_between(self, head: Optional[ListNode], left: int, right: int) -> Optional[ListNode]:
        if head is None or left == right:
            return head
    
        # Locate the node at position `left`
        prev_left = None
        left_node = head
        for _ in range(1, left):
            prev_left = left_node
            left_node = left_node.next

        # Locate the node after position `right`
        after_right = left_node
        for _ in range(right - left + 1):
            after_right = after_right.next
   
        # Reverse the sublist [left, right]
        prev = after_right
        curr = left_node
        for _ in range(right - left + 1):
            next_node = curr.next
            curr.next = prev

            prev = curr
            curr = next_node

        # Reconnect the reversed sublist
        if prev_left is not None:
            prev_left.next = prev
            return head

        # left == 1: new head is the start of reversed segment
        return prev
```
