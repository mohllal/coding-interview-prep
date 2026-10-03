---
title: Connect Level Order Siblings
difficulty: Medium
leetcode_title: Populating Next Right Pointers in Each Node
leetcode: https://leetcode.com/problems/populating-next-right-pointers-in-each-node/
tags:
  - Linked List
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Connect Level Order Siblings

## Problem description

Given the root of a binary tree where every node has an extra `next` pointer, connect each node to its level order successor — the next node to its right on the **same** level. The last node of each level should point to `null`.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1 → null
      /   \
     2  →  3 → null
    / \   / \
   4 → 5→6 → 7 → null

Output:
[1 -> null]
[2 -> 3 -> null]
[4 -> 5 -> 6 -> 7 -> null]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12 → null
       /  \
      7 →  1 → null
     /    / \
    9 → 10 → 5 → null

Output:
[12 -> null]
[7 -> 1 -> null]
[9 -> 10 -> 5 -> null]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 2^12 - 1]`.
- `-1000 <= Node.val <= 1000`
