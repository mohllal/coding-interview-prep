---
title: Level Averages in a Binary Tree
difficulty: Easy
leetcode_title: Average of Levels in Binary Tree
leetcode: https://leetcode.com/problems/average-of-levels-in-binary-tree/
tags:
  - Tree
  - Depth-First Search
  - Breadth-First Search
  - Binary Tree
---

# Level Averages in a Binary Tree

## Problem description

Given a binary tree, populate an array to represent the averages of all of its levels.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1
      /   \
     2     3
    / \   / \
   4   5 6   7

Output: [1.0, 2.5, 5.5]
Explanation: (1) / 1, (2 + 3) / 2, (4 + 5 + 6 + 7) / 4
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: [12.0, 4.0, 8.0]
Explanation: (12) / 1, (7 + 1) / 2, (9 + 10 + 5) / 3
```

## Constraints

- The number of nodes in the tree is in the range `[1, 10^4]`.
- `-2^31 <= Node.val <= 2^31 - 1`

## Hints

<details>
<summary>Hint 1</summary>

You don't need to store a level's values to average them — a running sum and a count are enough.

</details>

<details>
<summary>Hint 2</summary>

In a level-by-level BFS, the count is already known before you start the level.

</details>

## Solution

### Intuition

Run the standard level-by-level BFS, but instead of collecting each level's values, keep a running sum. The level size recorded at the start of the round is exactly the count, so the average is `level_sum / level_size`.

```plaintext
level 0   size=1   sum=12            avg=12.0
level 1   size=2   sum=7+1=8         avg=4.0
level 2   size=3   sum=9+10+5=24     avg=8.0
```

### Algorithm

1. Start a BFS from the root.
2. For each level, record `level_size`, then dequeue that many nodes while adding their values to `level_sum` and enqueuing their children.
3. Append `level_sum / level_size` to the result.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(n)$ — the queue can hold a whole level, up to $n/2$ nodes; the output holds one number per level.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def average_of_levels(self, root: Optional[TreeNode]) -> List[float]:
        result = []
        queue = deque([root])

        while queue:
            level_size = len(queue)
            level_sum = 0
            for _ in range(level_size):
                node = queue.popleft()
                level_sum += node.val
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(level_sum / level_size)

        return result
```
