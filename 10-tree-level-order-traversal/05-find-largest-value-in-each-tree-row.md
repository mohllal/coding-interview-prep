---
title: Find Largest Value in Each Tree Row
difficulty: Medium
leetcode: https://leetcode.com/problems/find-largest-value-in-each-tree-row/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Find Largest Value in Each Tree Row

## Problem description

Given the root of a binary tree, return an array containing the largest value in each row of the tree (0-indexed).

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, null, 6]

      1
     / \
    2   3
   / \   \
  4   5   6

Output: [1, 3, 6]
Explanation: Row 0 is [1], row 1 is [2, 3], row 2 is [4, 5, 6].
```

**Example 2:**

```plaintext
Input: root = [7, 4, 8, 2, 5, null, 9, null, 3]

        7
       / \
      4   8
     / \   \
    2   5   9
     \
      3

Output: [7, 8, 9, 3]
Explanation: Row 0 is [7], row 1 is [4, 8], row 2 is [2, 5, 9], row 3 is [3].
```

**Example 3:**

```plaintext
Input: root = [10, 5]

    10
   /
  5

Output: [10, 5]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 10^4]`.
- `-2^31 <= Node.val <= 2^31 - 1`

## Hints

<details>
<summary>Hint 1</summary>

A "row" is just a level. Traverse level by level and reduce each level to a single number.

</details>

<details>
<summary>Hint 2</summary>

Values can be as small as $-2^{31}$, so starting a level's maximum at `0` is wrong. Start it at `-inf`.

</details>

## Solution

### Intuition

Run the standard level-by-level BFS and reduce each level to its maximum instead of collecting its values. Track a running maximum per level, seeded with `-inf` so negative values are handled.

```plaintext
level 0   [7]         max=7
level 1   [4, 8]      max=8
level 2   [2, 5, 9]   max=9
level 3   [3]         max=3

→ [7, 8, 9, 3]
```

### Algorithm

1. If the root is empty, return `[]`.
2. Start a BFS from the root.
3. For each level, set `level_max = -inf`, then dequeue `len(queue)` nodes, updating `level_max` and enqueuing children.
4. Append `level_max` to the result.

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
    def largest_values(self, root: Optional[TreeNode]) -> List[int]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level_max = float("-inf")
            for _ in range(len(queue)):
                node = queue.popleft()
                level_max = max(level_max, node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(level_max)

        return result
```
