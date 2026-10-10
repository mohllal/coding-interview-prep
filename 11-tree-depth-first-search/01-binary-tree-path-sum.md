---
title: Binary Tree Path Sum
difficulty: Easy
leetcode_title: Path Sum
leetcode: https://leetcode.com/problems/path-sum/
tags:
  - Tree
  - Depth-First Search
  - Binary Tree
---

# Binary Tree Path Sum

## Problem description

Given a binary tree and an integer `S`, return `true` if the tree has a root-to-leaf path such that the sum of all node values on that path equals `S`. Otherwise return `false`.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7], S = 10

        1
      /   \
     2     3
    / \   / \
   4   5 6   7

Output: true
Explanation: 1 -> 3 -> 6 = 10
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5], S = 23

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: true
Explanation: 12 -> 1 -> 10 = 23
```

**Example 3:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5], S = 16

Output: false
```

## Constraints

- The number of nodes in the tree is in the range `[0, 5000]`.
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Subtract the current node's value from `S` as you descend. What condition at a leaf tells you you've found a valid path?

</details>

<details>
<summary>Hint 2</summary>

A node is a leaf only when it has no children. Reaching a null pointer does not mean you found a leaf — check both conditions together.

</details>

## Solution 1: Recursive DFS

### Intuition

Subtract each node's value from the remaining target as DFS descends. At a leaf, the path is valid if and only if the remaining target is zero — meaning every value on the root-to-leaf route summed to exactly `S`.

```plaintext
S = 10
        1         remaining: 10 - 1 = 9
      /   \
     2     3      left: 9 - 2 = 7 | right: 9 - 3 = 6
    / \   / \
   4   5 6   7    right subtree: 6 - 6 = 0 at leaf → found!
```

Null branches return `false` immediately; non-leaf nodes recurse into both children and return `true` if either child finds a valid path.

### Algorithm

1. If the node is null, return `false`.
2. Subtract `node.val` from the remaining target.
3. If the node is a leaf and the remaining target is zero, return `true`.
4. Recurse into the left and right children; return `true` if either succeeds.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(h)$ — the call stack holds one root-to-leaf path at a time, where $h$ is the tree height. $O(\log n)$ for a balanced tree, $O(n)$ worst case.

```python
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def hasPath(self, root: Optional[TreeNode], target_sum: int) -> bool:
        if root is None:
            return False

        remaining = target_sum - root.val

        if root.left is None and root.right is None:
            return remaining == 0

        return self.hasPath(root.left, remaining) or self.hasPath(root.right, remaining)
```

## Solution 2: Iterative DFS

Each stack frame carries `(node, target)` to compute `curr_remaining` when popping a node from the stack. Pushing right before left ensures the left child is processed first, matching the recursive traversal order.

We can use this when the tree could be skewed deeply enough to overflow Python's call stack.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(h)$ — the explicit stack holds at most one path's worth of frames at a time.

```python
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def hasPath(self, root: Optional[TreeNode], target_sum: int) -> bool:
        if root is None:
            return False

        stack = [(root, target_sum)]
        while stack:
            node, target = stack.pop()
            curr_remaining = target - node.val

            if node.left is None and node.right is None and curr_remaining == 0:
                return True

            if node.right:
                stack.append((node.right, curr_remaining))
            if node.left:
                stack.append((node.left, curr_remaining))
        return False
```
