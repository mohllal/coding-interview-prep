---
title: Reverse Alternating K-element Sub-List
difficulty: Hard
leetcode_title: Reverse Nodes in k-Group
leetcode: https://leetcode.com/problems/reverse-nodes-in-k-group/
tags:
  - Linked List
  - Recursion
---

# Reverse Alternating K-element Sub-List

## Problem description

Given the head of a linked list and a number `k`, reverse every alternating `k` sized sub-list starting from the head.

If, in the end, you are left with a sub-list with less than `k` elements, reverse it too.

## Examples

**Example 1:**

```plaintext
Input: head = [1,2,3,4,5,6,7,8], k = 2
Output: [2,1,3,4,6,5,7,8]

Groups:  [1,2] [3,4] [5,6] [7,8]
         ^^^^^ skip  ^^^^^ skip
        reverse     reverse
```

## Constraints

- The number of nodes in the list is `n`.
- `1 <= k <= n`

## Hints

<details>
<summary>Hint 1</summary>

Same as reversing every group of `k`, except every other group is left untouched.

</details>

<details>
<summary>Hint 2</summary>

After reversing a group, walk forward `k` nodes without touching anything, then reverse again. The skipped walk still has to update your previous-group pointer.

</details>

## Solution

### Intuition

This is a variation of [Reverse Nodes in k-Group](./03-reverse-every-k-element-sub-list.md). Instead of reversing every group, we alternate: reverse the first group, skip the second, reverse the third, and so on.

The key difference is a `should_reverse` flag that toggles after each group. When skipping, we still traverse `k` nodes and update `prev_group_tail` so the next reversed group attaches in the right place.

```plaintext
[1,2,3,4,5,6,7,8], k=2

Round 1 — reverse [1,2]:
  new_head = 2,  prev_group_tail = 1
  state:   2 ──▶ 1 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ 6 ──▶ 7 ──▶ 8
                 ▲
           prev_group_tail

Round 2 — skip [3,4]:
  traverse to last node of group → prev_group_tail = 4
  state:   2 ──▶ 1 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ 6 ──▶ 7 ──▶ 8
                           ▲
                     prev_group_tail

Round 3 — reverse [5,6]:
  connect: prev_group_tail (4) .next = 6 (new head of reversed group)
  prev_group_tail = 5
  state:   2 ──▶ 1 ──▶ 3 ──▶ 4 ──▶ 6 ──▶ 5 ──▶ 7 ──▶ 8
                                         ▲
                                   prev_group_tail

Round 4 — skip [7,8]:
  traverse to last node → prev_group_tail = 8
  (5.next = 7 was already correct from the original list, no rewiring needed)

Final:   2 ──▶ 1 ──▶ 3 ──▶ 4 ──▶ 6 ──▶ 5 ──▶ 7 ──▶ 8
```

### Algorithm

1. Initialize `should_reverse = True` to reverse the first group
2. For each group, scan ahead to count available nodes
3. If `should_reverse`:
   - Reverse the group using the standard technique
   - Connect to previous group's tail
4. If skipping:
   - Just traverse `k` nodes without reversing
   - Update `prev_group_tail` to the last node of the skipped group
5. Toggle `should_reverse` and move to the next group

### Complexity analysis

- Time complexity: $O(n)$ - each node is visited at most twice
- Space complexity: $O(1)$ - only using a constant number of pointers

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverse_alt_k_group(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if head is None or k == 1:
            return head

        curr = head
        prev_group_tail = None
        new_head = None
        should_reverse = True

        while curr is not None:
            group_start = curr
            after_group = self._get_group_end(curr, k)

            if should_reverse:
                new_group_head = self._reverse(group_start, after_group)
                if prev_group_tail is not None:
                    prev_group_tail.next = new_group_head
                else:
                    new_head = new_group_head
                prev_group_tail = group_start
            else:
                prev_group_tail = self._advance(group_start, k - 1)

            should_reverse = not should_reverse
            curr = after_group

        return new_head

    def _get_group_end(self, start: ListNode, k: int) -> Optional[ListNode]:
        node = start
        for _ in range(k):
            if node is None:
                break
            node = node.next
        return node

    def _reverse(self, left: ListNode, after_right: Optional[ListNode]) -> ListNode:
        prev = after_right
        curr = left
        while curr is not after_right:
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
        return prev

    def _advance(self, start: ListNode, steps: int) -> ListNode:
        node = start
        for _ in range(steps):
            if node.next is None:
                break
            node = node.next
        return node
```
