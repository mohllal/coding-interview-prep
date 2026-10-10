---
title: Path with Maximum Sum
difficulty: Hard
leetcode_title: Binary Tree Maximum Path Sum
leetcode: https://leetcode.com/problems/binary-tree-maximum-path-sum/
tags:
  - Dynamic Programming
  - Tree
  - Depth-First Search
  - Binary Tree
---

# Path with Maximum Sum

## Problem description

Find the path with the maximum sum in a given binary tree. A path is a sequence of nodes where each consecutive pair is connected by an edge, and no node appears more than once. The path does not need to start or end at the root, and a path can consist of a single node.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3]

    1
   / \
  2   3

Output: 6
Explanation: path is 2->1->3, sum = 2+1+3 = 6
```

**Example 2:**

```plaintext
Input: root = [-3, -1, 2]

    -3
   /  \
 -1    2

Output: 2
Explanation: path is just [2]; the single best node beats every longer path
```

**Example 3:**

```plaintext
Input: root = [1, 2, 5, 3, 4, null, 6]

        1
       / \
      2   5
     / \   \
    3   4   6

Output: 18
Explanation: path 4->2->1->5->6 = 4+2+1+5+6 = 18
```

## Constraints

- The number of nodes in the tree is in the range `[1, 3 * 10^4]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

At each node, the best complete path through it combines one arm going left and one going right. But that path cannot extend upward to the node's parent — what value should be returned to the parent instead?

</details>

<details>
<summary>Hint 2</summary>

A negative subtree contribution makes the path worse. If the left or right arm has a negative gain, ignore it (contribute 0). This is equivalent to choosing not to extend into that subtree.

</details>
