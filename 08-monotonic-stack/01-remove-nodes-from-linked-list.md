---
title: Remove Nodes From Linked List
difficulty: Medium
leetcode: https://leetcode.com/problems/remove-nodes-from-linked-list/
tags:
  - Linked List
  - Stack
  - Recursion
  - Monotonic Stack
---

# Remove Nodes From Linked List

## Problem description

Given the head of a singly linked list, remove every node that has a node with a greater value anywhere to its right. Return the head of the modified list.

## Examples

**Example 1:**

```plaintext
Input:  5 → 3 → 7 → 4 → 2 → 1
Output: 7 → 4 → 2 → 1
Explanation: 5 and 3 are removed — both have 7 to their right.
```

**Example 2:**

```plaintext
Input:  1 → 2 → 3 → 4 → 5
Output: 5
Explanation: Every node except 5 has a larger value to its right.
```

**Example 3:**

```plaintext
Input:  5 → 4 → 3 → 2 → 1
Output: 5 → 4 → 3 → 2 → 1
Explanation: Already non-increasing — no node has a larger value to its right.
```

## Constraints

- `1 <= number of nodes <= 10^5`
- `1 <= Node.val <= 10^5`

## Hints

<details>
<summary>Hint 1</summary>

A node survives only if no greater value appears to its right. What does that say about the values of surviving nodes from left to right?

</details>

<details>
<summary>Hint 2</summary>

Think of the surviving nodes as a sequence you build left to right. When a new node arrives with a larger value than the previous survivor, that previous node must be removed. A structure that lets you inspect and remove the most recent survivor efficiently would help.

</details>

## Solution

### Intuition

The surviving nodes form a non-increasing sequence — each is the maximum of everything to its right. Build this sequence using a monotonic (non-increasing) stack: push each node, but first pop any node whose value is smaller than the current one, since those have just found something larger to their right.

```plaintext
Input: 5 → 3 → 7 → 4 → 2 → 1

Visit 5: stack = [5]
Visit 3: 3 < 5, push.         stack = [5, 3]
Visit 7: 7 > 3 → pop 3 (found greater to right)
         7 > 5 → pop 5 (found greater to right)
         push 7.              stack = [7]
Visit 4: 4 < 7, push.         stack = [7, 4]
Visit 2: 2 < 4, push.         stack = [7, 4, 2]
Visit 1: 1 < 2, push.         stack = [7, 4, 2, 1]

Reconnect: 7 → 4 → 2 → 1
```

### Algorithm

1. Walk the list, pushing each node onto the stack
2. Before pushing, pop every node whose value is less than the current node's value
3. Reconnect the remaining nodes in order and return the new head

### Complexity analysis

- Time complexity: $O(n)$ — each node is pushed and popped at most once
- Space complexity: $O(n)$ — the stack

```python
from typing import Optional


class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


class Solution:
    def removeNodes(self, head: Optional[ListNode]) -> Optional[ListNode]:
        stack = []
        node = head

        while node:
            while stack and stack[-1].val < node.val:
                stack.pop()

            stack.append(node)
            node = node.next

        for i in range(len(stack) - 1):
            stack[i].next = stack[i + 1]

        stack[-1].next = None

        return stack[0] if stack else None
```
