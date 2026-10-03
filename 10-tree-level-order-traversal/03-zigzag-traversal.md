---
title: Zigzag Traversal
difficulty: Medium
leetcode_title: Binary Tree Zigzag Level Order Traversal
leetcode: https://leetcode.com/problems/binary-tree-zigzag-level-order-traversal/
tags:
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Zigzag Traversal

## Problem description

Given a binary tree, populate an array to represent its zigzag level order traversal: the first level is read left to right, the next level right to left, and the direction keeps alternating for every following level.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1               →
      /   \
     2     3            ←
    / \   / \
   4   5 6   7          →

Output: [[1], [3, 2], [4, 5, 6, 7]]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12              →
       /  \
      7    1            ←
     /    / \
    9    10  5          →

Output: [[12], [1, 7], [9, 10, 5]]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Changing the order in which you *enqueue* children gets complicated fast. What if the queue keeps working exactly as in a normal level order traversal, and only the way you *record* a level changes?

</details>

<details>
<summary>Hint 2</summary>

Build each level in a deque. On left-to-right levels, append to the back; on right-to-left levels, append to the front.

</details>

## Solution

### Intuition

Leave the BFS itself untouched — children are always enqueued left then right, so nodes always come off the queue left to right. Only the **recording** of each level alternates.

Build each level in a deque. On a left-to-right level, `append` each value to the back. On a right-to-left level, `appendleft` each value to the front; the first node dequeued ends up last, which reverses the level. Flip a boolean after every level.

```plaintext
level 0  dequeued: 12        left_to_right   level: [12]
level 1  dequeued: 7, 1      right_to_left   appendleft 7 → [7]
                                             appendleft 1 → [1, 7]
level 2  dequeued: 9, 10, 5  left_to_right   level: [9, 10, 5]

→ [[12], [1, 7], [9, 10, 5]]
```

### Algorithm

1. If the root is empty, return `[]`.
2. Start a BFS from the root with `left_to_right = True`.
3. For each level, dequeue `len(queue)` nodes. Append each value to the back of the level deque if `left_to_right`, otherwise to the front. Enqueue children left then right as usual.
4. Append the level to the result and flip `left_to_right`.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once, and both `append` and `appendleft` are $O(1)$.
- Space complexity: $O(n)$ — the output holds every value; the queue holds at most one level.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def zigzag_level_order(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        result = []
        queue = deque([root])
        left_to_right = True

        while queue:
            level = deque()
            for _ in range(len(queue)):
                node = queue.popleft()
                if left_to_right:
                    level.append(node.val)
                else:
                    level.appendleft(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(list(level))
            left_to_right = not left_to_right

        return result
```
