---
title: Sum of Path Numbers
difficulty: Medium
leetcode_title: Sum Root to Leaf Numbers
leetcode: https://leetcode.com/problems/sum-root-to-leaf-numbers/
tags:
  - Tree
  - Depth-First Search
  - Binary Tree
---

# Sum of Path Numbers

## Problem description

Given a binary tree where each node holds a single digit (0–9), each root-to-leaf path represents a number formed by concatenating the digits along that path. Find the total sum of all such numbers.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 7, 9, null, null, 2, 9]

        1
       / \
      7   9
         / \
        2   9

Output: 408
Explanation: paths are 17, 192, 199 → 17 + 192 + 199 = 408
```

**Example 2:**

```plaintext
Input: root = [1, 0, 1, null, null, 6, 5]

        1
       / \
      0   1
         / \
        6   5

Output: 126
Explanation: paths are 10, 116, 115 → 10 + 116 + 115 = 141
```

**Example 3:**

```plaintext
Input: root = [1, 2, 3]

    1
   / \
  2   3

Output: 25
Explanation: 12 + 13 = 25
```

## Constraints

- The number of nodes in the tree is in the range `[1, 1000]`.
- `0 <= Node.val <= 9`
- The depth of the tree will not exceed `10`.

## Hints

<details>
<summary>Hint 1</summary>

At each node, the number formed so far is `current_number * 10 + node.val`. How does this number change as you move from a parent to a child?

</details>

<details>
<summary>Hint 2</summary>

You never need to store the full path — the running number carries all the information you need. At a leaf, just add it to the total.

</details>

## Solution

### Intuition

Pass the number built so far down through the recursion. Each level shifts the current number left by one decimal place (`* 10`) and appends the current digit. At a leaf, the running number is one complete path-number and can be added to the total.

```plaintext
root = [1, 7, 9, _, _, 2, 9]

        1        running: 0*10+1 = 1
       / \
      7   9      left: 1*10+7 = 17 (leaf → add 17)
         / \     right: 1*10+9 = 19
        2   9    left leaf: 19*10+2 = 192 → add 192
                 right leaf: 19*10+9 = 199 → add 199

total: 17 + 192 + 199 = 408
```

### Algorithm

1. If the node is null, return 0.
2. Compute `running = current_number * 10 + node.val`.
3. If the node is a leaf, return `running`.
4. Otherwise, return the sum of recursive results from left and right children.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited exactly once.
- Space complexity: $O(h)$ — the call stack depth equals the tree height.

```python
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def sumNumbers(self, root: Optional[TreeNode]) -> int:
        return self._dfs(root, 0)

    def _dfs(self, node: Optional[TreeNode], current_number: int) -> int:
        if node is None:
            return 0

        current_number = current_number * 10 + node.val

        if node.left is None and node.right is None:
            return current_number

        return self._dfs(node.left, current_number) + self._dfs(node.right, current_number)
```
