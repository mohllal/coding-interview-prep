---
title: Rotate a Linked List
difficulty: Medium
leetcode_title: Rotate List
leetcode: https://leetcode.com/problems/rotate-list/
tags:
  - Linked List
  - Two Pointers
---

# Rotate a Linked List

## Problem description

Given the `head` of a linked list, rotate the list to the right by `k` places.

## Examples

**Example 1:**

```plaintext
Input: head = [1,2,3,4,5], k = 2
Output: [4,5,1,2,3]

Original: 1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5
                      ↑ cut here
Rotated:  4 ──▶ 5 ──▶ 1 ──▶ 2 ──▶ 3
```

**Example 2:**

```plaintext
Input: head = [0,1,2], k = 4
Output: [2,0,1]

k = 4 % 3 = 1 (effective rotation)
```

## Constraints

- The number of nodes in the list is in the range `[0, 500]`.
- `-100 <= Node.val <= 100`
- `0 <= k <= 2 * 10⁹`

## Hints

<details>
<summary>Hint 1</summary>

Rotating by the list's length changes nothing, so a huge `k` is not a problem — reduce it first.

</details>

<details>
<summary>Hint 2</summary>

Connecting the tail to the head makes a ring. Then the whole task is choosing where to break it, which is a fixed number of steps from the head.

</details>

## Solution

### Intuition

Rotating right by `k` means taking the last `k` nodes and moving them to the front. The key insight is that rotating by `length` returns the original list, so we only need to rotate by `k % length`.

The problem reduces to: find the cut point at position `length - k`, break the list there, and rewire the tail to point to the original head.

```plaintext
[1,2,3,4,5], k=2

Step 1 — traverse to find length and tail:

  1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null
                              ▲
                           tail (length=5)

Step 2 — effective rotations: k % length = 2 % 5 = 2
          new_tail is at position length - rotations = 5 - 2 = 3

  1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null
              ▲              ▲
           new_tail         tail

Step 3 — cut and rewire:
          new_head = new_tail.next = 4
          new_tail.next = null
          tail.next = head (old head, node 1)

  4 ──▶ 5 ──▶ 1 ──▶ 2 ──▶ 3 ──▶ null
  ▲                        ▲
new_head                new_tail
```

### Algorithm

1. Count the length and find the tail node
2. Compute effective rotation: `k % length` (if 0, return as-is)
3. Find the new tail at position `length - k` from head
4. Set `new_head = new_tail.next`
5. Rewire: `new_tail.next = null`, `tail.next = head`
6. Return `new_head`

### Complexity analysis

- Time complexity: $O(n)$ - two passes at most (count + find cut point)
- Space complexity: $O(1)$ - only using a few pointers

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def rotate_right(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if head is None or head.next is None or k == 0:
            return head

        length, tail = self._count_and_find_tail(head)

        rotations = k % length
        if rotations == 0:
            return head

        new_tail = self._advance(head, length - rotations - 1)
        new_head = new_tail.next

        new_tail.next = None
        tail.next = head

        return new_head

    def _count_and_find_tail(self, head: ListNode) -> tuple[int, ListNode]:
        length = 1
        node = head
        while node.next is not None:
            node = node.next
            length += 1
        return length, node

    def _advance(self, start: ListNode, steps: int) -> ListNode:
        node = start
        for _ in range(steps):
            node = node.next
        return node
```
