---
title: Reverse Level Order Traversal
difficulty: Medium
leetcode_title: Binary Tree Level Order Traversal II
leetcode: https://leetcode.com/problems/binary-tree-level-order-traversal-ii/
tags:
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Reverse Level Order Traversal

## Problem description

Given the root of a binary tree, return the bottom-up level order traversal of its nodes' values: the lowest level comes first, and each level is still read left to right.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1
      /   \
     2     3
    / \   / \
   4   5 6   7

Output: [[4, 5, 6, 7], [2, 3], [1]]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, null, 9, 10, 5]

        12
       /  \
      7    1
       \  / \
        9 10 5

Output: [[9, 10, 5], [7, 1], [12]]
```

**Example 3:**

```plaintext
Input: root = [6, 5, 2, null, null, 1, 6, 3, 56, 3]

        6
       / \
      5   2
         / \
        1   6
       / \  /
      3  56 3

Output: [[3, 56, 3], [1, 6], [5, 2], [6]]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

The order of values *within* a level does not change — only the order of the levels themselves. Can you still traverse top-down?

</details>

<details>
<summary>Hint 2</summary>

Inserting each finished level at the **front** of the result reverses the level order for free. Which structure makes front-insertion $O(1)$?

</details>

## Solution

### Intuition

This is the ordinary top-down level order traversal; only the order in which finished levels are stored changes. Each level is still built left to right, but instead of appending it to the end of the result, we push it to the **front**. The last level discovered ends up first.

A `deque` makes front-insertion $O(1)$; inserting at index 0 of a list would shift every earlier level each time.

```plaintext
level [1]            result: [[1]]
level [2, 3]         result: [[2, 3], [1]]
level [4, 5, 6, 7]   result: [[4, 5, 6, 7], [2, 3], [1]]
```

### Algorithm

1. If the root is empty, return `[]`.
2. Run a level-by-level BFS, recording `len(queue)` at the start of each level.
3. Push each completed level onto the **front** of a deque.
4. Return the deque as a list.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once, and each front-insertion into the deque is $O(1)$.
- Space complexity: $O(n)$ — the output holds every value and the queue holds at most one level.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def level_order_bottom(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        result = deque()
        queue = deque([root])

        while queue:
            level = []
            for _ in range(len(queue)):
                node = queue.popleft()
                level.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.appendleft(level)

        return list(result)
```
