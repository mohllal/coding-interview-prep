---
title: Maximum Level Sum of a Binary Tree
difficulty: Medium
leetcode: https://leetcode.com/problems/maximum-level-sum-of-a-binary-tree/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Maximum Level Sum of a Binary Tree

## Problem description

You are given the root of a binary tree. The level of the root is `1`, the level of its children is `2`, and so on.

Return the level `x` where the sum of all node values is the highest. If several levels share the maximum sum, return the smallest such `x`.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 20, 3, 4, 5, null, 8]

      1
     / \
   20   3
   / \   \
  4   5   8

Output: 2
Explanation:
Level 1: [1]         sum = 1
Level 2: [20, 3]     sum = 23
Level 3: [4, 5, 8]   sum = 17
```

**Example 2:**

```plaintext
Input: root = [10, 5, -3, 3, 2, null, 11, 3, -2, null, 1]

          10
         /  \
        5    -3
       / \     \
      3   2     11
     / \   \
    3  -2   1

Output: 3
Explanation:
Level 1: [10]          sum = 10
Level 2: [5, -3]       sum = 2
Level 3: [3, 2, 11]    sum = 16
Level 4: [3, -2, 1]    sum = 2
```

**Example 3:**

```plaintext
Input: root = [5, 6, 7, 8, null, null, 9, null, null, 10]

        5
       / \
      6   7
     /     \
    8       9
           /
          10

Output: 3
Explanation:
Level 1: [5]      sum = 5
Level 2: [6, 7]   sum = 13
Level 3: [8, 9]   sum = 17
Level 4: [10]     sum = 10
```

## Constraints

- The number of nodes in the tree is in the range `[1, 10^4]`.
- `-10^5 <= Node.val <= 10^5`

## Hints

<details>
<summary>Hint 1</summary>

Compute each level's sum with a level-by-level BFS, and keep the best one seen so far along with its level number.

</details>

<details>
<summary>Hint 2</summary>

Ties go to the smaller level. Since levels are visited top-down, update the best only on a **strictly** greater sum.

</details>

## Solution

### Intuition

Run the standard level-by-level BFS, summing each level as you go, and remember which level produced the largest sum.

Two details matter. Values can be negative, so the best sum must start at `-inf`, not `0`. And on a tie the earlier level wins — since BFS visits levels top-down, replacing the best only when a sum is **strictly** larger keeps the smallest level automatically.

```plaintext
level 1   sum=10    best=(10, level 1)
level 2   sum=2     2 > 10?  no
level 3   sum=16    16 > 10? yes → best=(16, level 3)
level 4   sum=2     2 > 16?  no

→ 3
```

### Algorithm

1. Start a BFS from the root with `level = 0`, `best_sum = -inf`, `best_level = 1`.
2. For each level, increment `level`, then dequeue `len(queue)` nodes, summing their values and enqueuing children.
3. If `level_sum > best_sum`, record `best_sum = level_sum` and `best_level = level`.
4. Return `best_level`.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
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
    def max_level_sum(self, root: Optional[TreeNode]) -> int:
        queue = deque([root])
        level = 0
        best_sum, best_level = float("-inf"), 1

        while queue:
            level += 1
            level_sum = 0
            for _ in range(len(queue)):
                node = queue.popleft()
                level_sum += node.val
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            if level_sum > best_sum:
                best_sum, best_level = level_sum, level

        return best_level
```
