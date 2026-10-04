---
title: Linked List Cycle
difficulty: Easy
leetcode: https://leetcode.com/problems/linked-list-cycle/
tags:
  - Hash Table
  - Linked List
  - Two Pointers
  - Floyd's Cycle Finding Algorithm
---

# Linked List Cycle

## Problem description

Given `head`, the head of a linked list, determine if the linked list has a cycle in it.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to. **Note that `pos` is not passed as a parameter**.

Return `true` if there is a cycle in the linked list. Otherwise, return `false`.

## Examples

**Example 1:**

```plaintext
3 ──▶ 2 ──▶ 0 ──▶ -4
      ▲            │
      └────────────┘

Input: head = [3,2,0,-4], pos = 1
Output: true
Explanation: There is a cycle in the linked list, where the tail connects to the 1st node (0-indexed).
```

**Example 2:**

```plaintext
┌─────────┐
▼         │
1 ──▶ 2 ──┘

Input: head = [1,2], pos = 0
Output: true
Explanation: There is a cycle in the linked list, where tail connects to the 0th node.
```

**Example 3:**

```plaintext
1 ──▶ null

Input: head = [1], pos = -1
Output: false
Explanation: There is no cycle in the linked list.
```

## Constraints

- The number of the nodes in the list is in the range `[0, 10^4]`.
- `-10^5 <= Node.val <= 10^5`
- `pos` is `-1` or a valid index in the linked-list.

## Hints

<details>
<summary>Hint 1</summary>

A hash set of visited nodes works in $O(n)$ space. To get to $O(1)$, think about two runners on a circular track.

</details>

<details>
<summary>Hint 2</summary>

If one pointer moves twice as fast as the other, the gap between them closes by exactly one node per step. On a finite loop, a collision is unavoidable.

</details>

## Solution

### Intuition

Two runners on a circular track at different speeds will eventually meet. The fast pointer moves 2 steps while slow moves 1, so fast gains 1 step per iteration. In a cycle, this gap shrinks until they collide. Without a cycle, fast reaches the end first.

**With a cycle**:

```plaintext
3 ──▶ 2 ──▶ 0 ──▶ -4
      ▲            │
      └────────────┘

             slow   fast
start          3      3
iteration 1    2      0
iteration 2    0      2      fast wrapped: 0 → -4 → 2
iteration 3   -4     -4      ← same node: cycle detected
```

**Without a cycle:**

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ null

             slow   fast
start          1      1
iteration 1    2      3
iteration 2    3      null   ← fast fell off the end: no cycle
```

### Algorithm

1. Initialize slow and fast pointers at head
2. Move slow one step, fast two steps each iteration
3. If they meet, there is a cycle
4. If fast reaches null, there is no cycle

### Complexity analysis

- Time complexity: $O(n)$ - Each node is visited at most twice
- Space complexity: $O(1)$ - Only two pointers used regardless of list size

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def has_cycle(self, head: Optional[ListNode]) -> bool:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

            if slow == fast:
                return True

        return False
```
