---
title: Tree Diameter
difficulty: Medium
leetcode_title: Diameter of Binary Tree
leetcode: https://leetcode.com/problems/diameter-of-binary-tree/
tags:
  - Tree
  - Depth-First Search
  - Binary Tree
---

# Tree Diameter

## Problem description

Given a binary tree, find the length of its diameter. The diameter is the number of nodes on the longest path between any two leaf nodes. The path does not need to pass through the root.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 5, 3, 4, null, 6]

        1
       / \
      2   5
     / \   \
    3   4   6

Output: 5
Explanation: longest path is 3->2->1->5->6, which has 5 nodes.
```

**Example 2:**

```plaintext
Input: root = [1, 2, 3, null, null, 4, 5, 6, null, null, 7, 8]

        1
       / \
      2   3
         / \
        4   5
       /     \
      6       7
               \
                8

Output: 6
Explanation: longest path is 6->4->3->5->7->8, which has 6 nodes.
```

## Constraints

- The number of nodes in the tree is in the range `[1, 10^4]`.
- `-100 <= Node.val <= 100`

## Hints

<details>
<summary>Hint 1</summary>

The longest path through a given node uses one branch going left and one going right. What does each recursive call need to return to its parent, versus what it needs to update globally?

</details>

<details>
<summary>Hint 2</summary>

There are two roles: the answer (the longest complete path, touching both children) and the return value (the longest single arm, which the parent can extend). They are different things — keep them separate.

</details>

## Solution 1: Return height

### Intuition

At every node, the longest path passing through it is the sum of the two longest branches descending from it (one left, one right). But that path cannot extend upward — a path can only bend once.

So each recursive call serves two purposes:

1. Update the global diameter candidate using `left_height + right_height` (the longest path through the current node, in node count).
2. Return `1 + max(left_height, right_height)` — the longest single arm the parent can extend.

```plaintext
        1
       / \
      2   5
     / \   \
    3   4   6

node 3: left=0, right=0  → diameter candidate: 1, return 1
node 4: left=0, right=0  → diameter candidate: 1, return 1
node 2: left=1, right=1  → diameter candidate: 1+1+1=3, return 1+1=2
node 6: left=0, right=0  → diameter candidate: 1, return 1
node 5: left=0, right=1  → diameter candidate: 0+1+1=2, return 1+1=2
node 1: left=2, right=2  → diameter candidate: 2+2+1=5, return 2+1=3
```

Note: LeetCode's version counts edges, not nodes, so its answer is `diameter - 1`. This problem counts nodes.

### Algorithm

1. Recursively compute the height of each subtree.
2. At each node, update the global diameter with `left_height + right_height + 1` (node count on the path through this node).
3. Return `1 + max(left_height, right_height)` as the arm length available to the parent.

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
    def findDiameter(self, root: Optional[TreeNode]) -> int:
        self.diameter = 0
        self._height(root)
        return self.diameter

    def _height(self, node: Optional[TreeNode]) -> int:
        if node is None:
            return 0

        left_height = self._height(node.left)
        right_height = self._height(node.right)

        # path through this node uses both arms (counted in nodes)
        curr_diameter = left_height + right_height + 1
        self.diameter = max(self.diameter, curr_diameter)

        return 1 + max(left_height, right_height)
```

## Solution 2: Pass depth down

### Intuition

Instead of computing heights bottom-up, pass each node its absolute depth from the root and return the maximum absolute depth reachable in that subtree. The diameter through the current node is then the sum of the two arm lengths — which are just the returned depths minus the current depth — plus one for the node itself:

$$\text{diameter} = (\text{left\_depth} - \text{depth}) + (\text{right\_depth} - \text{depth}) + 1$$

$$= \text{left\_depth} + \text{right\_depth} - 2 \cdot \text{depth} + 1$$

The null sentinel returns `depth - 1` (one level shallower than the null's own depth), which equals the parent's depth. This makes the formula work cleanly at leaves: a leaf's two null children both return `depth`, so its arm lengths are each `depth - depth = 0` and its diameter candidate is `0 + 0 + 1 = 1` — correct.

```plaintext
        1          depth=0  → left_depth=2, right_depth=2  → diameter=2+2-0+1=5 ✓
       / \
      2   5        depth=1
     / \   \
    3   4   6      depth=2  → leaf: left=2, right=2         → diameter=2+2-4+1=1 ✓
```

### Algorithm

1. If the node is null, return `depth - 1` (the parent's depth).
2. Recurse into both children, passing `depth + 1`.
3. Update the global diameter with `left_depth + right_depth - 2 * depth + 1`.
4. Return `max(left_depth, right_depth)` — the deepest absolute depth reachable from this subtree.

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
    def findDiameter(self, root: Optional[TreeNode]) -> int:
        self.diameter = 0
        self._dfs(root, 0)
        return self.diameter

    def _dfs(self, node: Optional[TreeNode], depth: int) -> int:
        if node is None:
            # no node here; the deepest reachable depth is the parent's depth
            return depth - 1

        left_depth = self._dfs(node.left, depth + 1)
        right_depth = self._dfs(node.right, depth + 1)

        # (left_depth - depth) + (right_depth - depth) + 1
        curr_diameter = left_depth + right_depth - (2 * depth) + 1
        self.diameter = max(self.diameter, curr_diameter)

        return max(left_depth, right_depth)
```
