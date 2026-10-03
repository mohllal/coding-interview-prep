---
title: N-ary Tree Level Order Traversal
difficulty: Medium
leetcode: https://leetcode.com/problems/n-ary-tree-level-order-traversal/
tags:
  - Tree
  - Breadth-First Search
---

# N-ary Tree Level Order Traversal

## Problem description

Given an n-ary tree, return the level order traversal of its nodes' values, with each level in its own sub-array.

Examples write the tree as an array in level order, where each node's group of children is separated by a `null`. You receive the root of the tree, already built — you don't parse the array yourself.

## Examples

**Example 1:**

```plaintext
Input: root = [1, null, 2, 3, 4, null, 5, 6]

        1
      / | \
     2  3  4
    / \
   5   6

Output: [[1], [2, 3, 4], [5, 6]]
```

**Example 2:**

```plaintext
Input: root = [7, null, 3, 8, 5, null, 2, 9, null, 6, null, 1, 4, 10]

            7
         /  |  \
        3   8   5
       / \  |  /|\
      2  9  6 1 4 10

Output: [[7], [3, 8, 5], [2, 9, 6, 1, 4, 10]]
```

**Example 3:**

```plaintext
Input: root = [10, null, 15, 12, null, 20, null, 25, null, 30, 40]

        10
       /  \
     15    12
      |     |
     20    25
    /  \
   30  40

Output: [[10], [15, 12], [20, 25], [30, 40]]
```

## Constraints

- The height of the n-ary tree is at most `1000`.
- The number of nodes is in the range `[0, 10^4]`.
