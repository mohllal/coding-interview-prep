---
title: Reverse Every K-element Sub-List
difficulty: Hard
leetcode_title: Reverse Nodes in k-Group
leetcode: https://leetcode.com/problems/reverse-nodes-in-k-group/
tags:
  - Linked List
  - Recursion
---

# Reverse Every K-element Sub-List

## Problem description

Given the `head` of a linked list, reverse the nodes of the list `k` at a time, and return the modified list.

`k` is a positive integer and is less than or equal to the length of the linked list. If the number of nodes is not a multiple of `k` then left-out nodes, in the end, should remain as it is.

You may not alter the values in the list's nodes, only nodes themselves may be changed.

## Examples

**Example 1:**

```plaintext
Input: head = [1,2,3,4,5], k = 2
Output: [2,1,4,3,5]
```

**Example 2:**

```plaintext
Input: head = [1,2,3,4,5], k = 3
Output: [3,2,1,4,5]
```

## Constraints

- The number of nodes in the list is `n`.
- `1 <= k <= n <= 5000`
- `0 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Reverse one group of `k`, then repeat. The difficulty is connecting each reversed group to the one before and after it.

</details>

<details>
<summary>Hint 2</summary>

After reversing a group, its original head becomes its tail — and that tail is what must point at the next group. Keep it as the previous-group pointer for the following round.

</details>

## Solution

### Intuition

This problem applies the [sub-list reversal technique](./02-reverse-a-sub-list.md) repeatedly for each group of `k` nodes.

For each group of `k` nodes, we reverse it and connect it to the previous group's tail. After reversing, the original first node of the group becomes its tail — save it as `prev_group_tail` so the next reversed group can attach to it.

```plaintext
[1,2,3,4,5], k=2

Round 1 — reverse group [1,2], seed prev = after_group = 3:

  iter 1   1.next = 3     prev=1, curr=2
  iter 2   2.next = 1     prev=2, curr=3

  new_head = 2,  prev_group_tail = 1 (original first node, now the tail)

  state:   2 ──▶ 1 ──▶ 3 ──▶ 4 ──▶ 5
                 ▲
           prev_group_tail

Round 2 — reverse group [3,4], seed prev = after_group = 5:

  iter 1   3.next = 5     prev=3, curr=4
  iter 2   4.next = 3     prev=4, curr=5

  connect: prev_group_tail (1) .next = prev (4)
  prev_group_tail = 3

  state:   2 ──▶ 1 ──▶ 4 ──▶ 3 ──▶ 5
                           ▲
                     prev_group_tail

Round 3 — only 1 node left (5), count < k=2 → stop, leave as-is

Final:   2 ──▶ 1 ──▶ 4 ──▶ 3 ──▶ 5
```

### Algorithm

1. For each potential group, scan ahead to check if `k` nodes exist
2. If fewer than `k` nodes remain, stop — they stay unchanged
3. Reverse the group: seed `prev = after_group`, iterate `k` times
4. Connect to the previous group's tail
5. Save the original group start (now its tail) and advance to the next group

### Complexity analysis

- Time complexity: $O(n)$ — each node is visited twice (once in the scan, once in the reversal)
- Space complexity: $O(1)$ — only a constant number of pointers

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverse_k_group(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        if head is None or k == 1:
            return head

        curr = head
        prev_group_tail = None
        new_head = None

        while curr is not None:
            group_start = curr
            after_group = self._get_group_end(curr, k)

            if after_group is None:
                break  # fewer than k nodes remain — partial tail stays as-is

            new_group_head = self._reverse(group_start, after_group, k)

            if prev_group_tail is not None:
                prev_group_tail.next = new_group_head
            else:
                new_head = new_group_head

            prev_group_tail = group_start  # original start is now the tail
            curr = after_group

        return new_head

    def _get_group_end(self, start: ListNode, k: int) -> Optional[ListNode]:
        node = start
        for _ in range(k):
            if node is None:
                return None  # fewer than k nodes available
            node = node.next
        return node  # node right after the k-th element

    def _reverse(self, left: ListNode, after_right: Optional[ListNode], k: int) -> ListNode:
        prev = after_right
        curr = left
        for _ in range(k):
            next_node = curr.next
            curr.next = prev
            prev = curr
            curr = next_node
        return prev  # new head of the reversed group
```
