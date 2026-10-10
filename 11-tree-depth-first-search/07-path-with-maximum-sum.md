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

## Solution

### Intuition

The same two-role structure as Tree Diameter applies here, but with sums instead of heights:

- **Global answer**: the best complete path through the current node, using both children. This equals `left_gain + node.val + right_gain` where `left_gain = max(0, best_arm_left)` and `right_gain = max(0, best_arm_right)`. Clamping to zero drops subtrees with negative sums.
- **Return value**: the best single arm the parent can extend — `node.val + max(left_gain, right_gain)`. A path can only bend once, so only one child's arm is passed upward.

```plaintext
root = [1, 2, 5, 3, 4, null, 6]

        1
       / \
      2   5
     / \   \
    3   4   6

node 3: gain=3,  global max: 3
node 4: gain=4,  global max: 4
node 2: left=3, right=4  →  path: 3+2+4=9, arm: 2+4=6,   global max: 9
node 6: gain=6,  global max: 9
node 5: left=0, right=6  →  path: 0+5+6=11, arm: 5+6=11,  global max: 11
node 1: left=6, right=11 →  path: 6+1+11=18, arm: 1+11=12, global max: 18
```

### Algorithm

1. Recursively compute the best arm gain from each subtree, clamping to 0 when negative.
2. At each node, update the global maximum with `left_gain + node.val + right_gain`.
3. Return `node.val + max(left_gain, right_gain)` to the parent.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once.
- Space complexity: $O(h)$ — the call stack holds one root-to-leaf path.

```python
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def findMaximumPathSum(self, root: Optional[TreeNode]) -> int:
        self.max_sum = float("-inf")
        self._max_arm(root)
        return self.max_sum

    def _max_arm(self, node: Optional[TreeNode]) -> int:
        if node is None:
            return 0

        # clamp negative gains to 0 — a negative subtree only hurts the path
        left_gain = max(0, self._max_arm(node.left))
        right_gain = max(0, self._max_arm(node.right))

        # best complete path bending through this node
        self.max_sum = max(self.max_sum, left_gain + node.val + right_gain)

        # best single arm the parent can extend
        return node.val + max(left_gain, right_gain)
```
