---
title: Right View of a Binary Tree
difficulty: Medium
leetcode_title: Binary Tree Right Side View
leetcode: https://leetcode.com/problems/binary-tree-right-side-view/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Right View of a Binary Tree

## Problem description

Given the root of a binary tree, return an array containing the values of the nodes in its right view.

The right view is what you see looking at the tree from the right side: for each level, only the **last** node on that level is visible.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1        ← 1
      /   \
     2     3     ← 3
    / \   / \
   4   5 6   7   ← 7

Output: [1, 3, 7]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, null, 9, 10, 5, null, 3]

        12       ← 12
       /  \
      7    1     ← 1
       \  / \
        9 10 5   ← 5
         \
          3      ← 3

Output: [12, 1, 5, 3]
Explanation: 3 is visible from the right even though it hangs off the left subtree — nothing else is on its level.
```

**Example 3:**

```plaintext
Input: root = [8, 4, 9, 3, null, null, 10, 2]

        8        ← 8
       / \
      4   9      ← 9
     /     \
    3       10   ← 10
   /
  2              ← 2

Output: [8, 9, 10, 2]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 100]`.
- `-100 <= Node.val <= 100`

## Hints

<details>
<summary>Hint 1</summary>

"Only follow right children" fails on Example 2 — the bottom node 3 is in the left subtree. Think per level, not per path.

</details>

<details>
<summary>Hint 2</summary>

In a level-by-level BFS, you know the level size up front, so you know which dequeued node is the last one on its level.

</details>

## Solution

### Intuition

The right view is simply the **last node of every level**. Walking down right children alone doesn't work, because a level's rightmost node can live in the left subtree when the right subtree is shorter (node 3 in Example 2).

Run the standard level-by-level BFS. Nodes come off the queue left to right, so when the inner loop finishes, the node still in hand is the last one on that level — record it.

```plaintext
level 0   [12]          last → 12
level 1   [7, 1]        last → 1
level 2   [9, 10, 5]    last → 5
level 3   [3]           last → 3

→ [12, 1, 5, 3]
```

### Algorithm

1. If the root is empty, return `[]`.
2. Start a BFS from the root.
3. For each level, record `level_size` and dequeue that many nodes, enqueuing children left then right.
4. After the level's loop ends, append the value of the last dequeued node to the result.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(n)$ — the queue can hold a whole level, up to $n/2$ nodes.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def right_side_view(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            for _ in range(len(queue)):
                node = queue.popleft()
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            result.append(node.val)  # node is the last one dequeued on this level

        return result
```

## Relationship to [Left View of a Binary Tree](./14.1-left-view-of-a-binary-tree.md)

The left view is the mirror image: same traversal, but it reads the front of the queue **before** each level's loop (the first node waiting) instead of the node in hand **after** it (the last one dequeued).
