---
title: Maximum Width of Binary Tree
difficulty: Medium
leetcode: https://leetcode.com/problems/maximum-width-of-binary-tree/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Maximum Width of Binary Tree

## Problem description

Given the root of a binary tree, return the maximum width of the tree — the width of its widest level.

The width of a level is the number of positions between its leftmost and rightmost non-null nodes, **inclusive**, counting the null positions in between as if the tree were a complete binary tree down to that level.

The answer is guaranteed to fit in a 32-bit signed integer.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, null, null, 5]

      1
     / \
    2   3
   /     \
  4       5

Output: 4
Explanation: The last level spans four positions: [4, null, null, 5].
```

**Example 2:**

```plaintext
Input: root = [1, 2, 3, 4, null, 5, 6, null, 7]

        1
       / \
      2   3
     /   / \
    4   5   6
     \
      7

Output: 4
Explanation: Level 3 spans four positions: [4, null, 5, 6].
```

**Example 3:**

```plaintext
Input: root = [1, 2, null, 3, 4, null, null, 5]

      1
     /
    2
   / \
  3   4
     /
    5

Output: 2
Explanation: The widest level is [3, 4].
```

## Constraints

- The number of nodes in the tree is in the range `[1, 3000]`.
- `-100 <= Node.val <= 100`
