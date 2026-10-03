---
title: Level Order Successor
difficulty: Easy
tags:
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Level Order Successor

## Problem description

Given the root of a binary tree and an integer `key`, find the level order successor of the node containing `key`.

The level order successor is the node that appears **right after** the given node in the level order traversal. Return the successor node itself, not its value. Every input is guaranteed to have a successor.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5], key = 3

      1
     / \
    2   3
   / \
  4   5

Output: 4
Explanation: The level order traversal is [1, 2, 3, 4, 5]. The node after 3 is 4.
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5], key = 9

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: 10
Explanation: The level order traversal is [12, 7, 1, 9, 10, 5]. The node after 9 is 10.
```

**Example 3:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5], key = 12
Output: 7
Explanation: The level order traversal is [12, 7, 1, 9, 10, 5]. The node after 12 is 7.
```

## Constraints

- The number of nodes in the tree is in the range `[0, 10^5]`.
- `-1000 <= Node.val <= 1000`
