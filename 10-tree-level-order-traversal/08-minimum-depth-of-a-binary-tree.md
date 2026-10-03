---
title: Minimum Depth of a Binary Tree
difficulty: Easy
leetcode_title: Minimum Depth of Binary Tree
leetcode: https://leetcode.com/problems/minimum-depth-of-binary-tree/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Minimum Depth of a Binary Tree

## Problem description

Given the root of a binary tree, find its minimum depth.

The minimum depth is the number of nodes along the shortest path from the root down to the nearest **leaf** — a node with no children.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5]

      1
     / \
    2   3
   / \
  4   5

Output: 2
Explanation: 3 is the nearest leaf, on the path 1 → 3.
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: 3
Explanation: Every leaf (9, 10, 5) is three nodes from the root.
```

**Example 3:**

```plaintext
Input: root = [1, null, 2, null, 3]

  1
   \
    2
     \
      3

Output: 3
Explanation: 1 and 2 are not leaves — each has a child — so the only leaf is 3.
```

## Constraints

- The number of nodes in the tree is in the range `[0, 10^5]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

BFS visits nodes in order of their depth. What does that tell you about the **first** leaf it meets?

</details>

<details>
<summary>Hint 2</summary>

Look at Example 3. A node with only one child is not a leaf, so a missing child must not count as a path of depth 0.

</details>

## Solution

### Intuition

BFS reaches every node at depth `d` before any node at depth `d + 1`. So the **first leaf** it dequeues is the shallowest one, and its level is the answer. The rest of the tree never needs to be explored.

The one trap is the definition of a leaf: a node with **no** children. A node with a single child is not a leaf, so in Example 3 neither 1 nor 2 can end the search — only 3 can.

```plaintext
root = [1, 2, 3, 4, 5]

      1
     / \
    2   3
   / \
  4   5

depth 1   dequeue 1   has children → enqueue 2, 3
depth 2   dequeue 2   has children → enqueue 4, 5
          dequeue 3   no children  → leaf, return 2
```

4 and 5 are never dequeued: the search stops at the first leaf.

### Algorithm

1. If the root is empty, return `0`.
2. Start a BFS from the root with `depth = 0`.
3. For each level, increment `depth`, then dequeue `len(queue)` nodes:
   - If the node has no children, return `depth`.
   - Otherwise enqueue its non-null children.

### Complexity analysis

- Time complexity: $O(n)$ — in the worst case (the shallowest leaf is on the last level, as in a perfect tree) every node is visited. Often far fewer are, because the search stops at the first leaf.
- Space complexity: $O(n)$ — the queue can hold a whole level, up to $n/2$ nodes.

```python
from collections import deque
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def min_depth(self, root: Optional[TreeNode]) -> int:
        if not root:
            return 0

        queue = deque([root])
        depth = 0

        while queue:
            depth += 1
            for _ in range(len(queue)):
                node = queue.popleft()
                if not node.left and not node.right:
                    return depth
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

        return depth
```

## Relationship to [Maximum Depth of a Binary Tree](./08.1-maximum-depth-of-a-binary-tree.md)

Same level-counting BFS, opposite stopping rule. Minimum depth returns at the **first** leaf it dequeue while maximum depth needs the **last** level, so it can't stop early and always visits every node.
