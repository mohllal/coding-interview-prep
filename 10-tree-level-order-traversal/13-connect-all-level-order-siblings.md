---
title: Connect All Level Order Siblings
difficulty: Medium
tags:
  - Linked List
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Connect All Level Order Siblings

## Problem description

Given the root of a binary tree where every node has an extra `next` pointer, connect each node to its level order successor. Unlike connecting siblings within a level, the last node of each level points to the **first node of the next level**, so the whole tree becomes one chain. The very last node points to `null`.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1
      /   \
     2     3
    / \   / \
   4   5 6   7

Output: 1 -> 2 -> 3 -> 4 -> 5 -> 6 -> 7 -> null
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: 12 -> 7 -> 1 -> 9 -> 10 -> 5 -> null
```

## Constraints

- The number of nodes in the tree is in the range `[0, 2^12 - 1]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

The chain should follow the plain level order traversal, across level boundaries. Do you even need to know where a level ends?

</details>

<details>
<summary>Hint 2</summary>

Keep a pointer to the previously dequeued node for the entire traversal, and link it to the current node every time.

</details>

## Solution

### Intuition

The required chain is the level order traversal itself, so the `next` pointers just follow the order in which nodes leave the queue. Unlike connecting siblings within a level, the chain never breaks at a level boundary, so level sizes don't matter here. A plain BFS works.

Keep `previous`, the last node dequeued. Each time a node comes off the queue, link `previous.next` to it, then make it the new `previous`. The final node keeps its default `next = None`.

```plaintext
dequeue 12   previous=None  → nothing to link     previous=12
dequeue 7    12.next = 7                          previous=7
dequeue 1    7.next  = 1                          previous=1
dequeue 9    1.next  = 9    ← crosses a level     previous=9
dequeue 10   9.next  = 10                         previous=10
dequeue 5    10.next = 5                          previous=5

5.next stays None → 12 -> 7 -> 1 -> 9 -> 10 -> 5 -> null
```

### Algorithm

1. If the root is empty, return it.
2. Start a BFS from the root with `previous = None`.
3. For each dequeued node:
   - If `previous` exists, set `previous.next = node`.
   - Set `previous = node` and enqueue its children left then right.
4. Return the root.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(n)$ — the queue can hold a whole level, up to $n/2$ nodes.

```python
from collections import deque
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None, next=None):
        self.val = val
        self.left = left
        self.right = right
        self.next = next


class Solution:
    def connect_all_siblings(self, root: Optional[TreeNode]) -> Optional[TreeNode]:
        if not root:
            return root

        queue = deque([root])
        previous = None

        while queue:
            node = queue.popleft()
            if previous:
                previous.next = node
            previous = node

            if node.left:
                queue.append(node.left)
            if node.right:
                queue.append(node.right)

        return root
```
