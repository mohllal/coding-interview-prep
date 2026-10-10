---
title: All Paths for a Sum
difficulty: Medium
leetcode_title: Path Sum II
leetcode: https://leetcode.com/problems/path-sum-ii/
tags:
  - Backtracking
  - Tree
  - Depth-First Search
  - Binary Tree
---

# All Paths for a Sum

## Problem description

Given a binary tree and a number `S`, find all root-to-leaf paths such that the sum of all node values on each path equals `S`.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 7, 9, 4, 5, 2, 7], S = 12

        1
      /   \
     7     9
    / \   / \
   4   5 2   7

Output: [[1, 7, 4], [1, 9, 2]]
Explanation: 1+7+4=12 and 1+9+2=12
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 4, null, 10, 5], S = 23

        12
       /  \
      7    1
     /    / \
    4    10  5

Output: [[12, 7, 4], [12, 1, 10]]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 5000]`.
- `-1000 <= Node.val <= 1000`
- `-1000 <= targetSum <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Track the current root-to-node path in a list as you descend. At a leaf with the right remaining sum, save a copy. What must happen to the list before you return to the parent?

</details>

<details>
<summary>Hint 2</summary>

The same list object is shared throughout the recursion. If you append a node, recurse, and forget to remove it, every later path will include stale entries from earlier branches.

</details>

## Solution 1: Recursive DFS with backtracking

### Intuition

Maintain a running path list as DFS descends, appending the current node on the way in and removing it on the way out (backtracking). At each leaf, check whether the accumulated sum equals `S`; if so, save a copy of the path.

```plaintext
S = 12
         1         path: [1], remaining: 11
       /   \
      7     9      left: [1,7], remaining: 4
     / \   / \
    4   5 2   7    leaf 4: [1,7,4], remaining: 0 → save copy
                   leaf 5: [1,7,5], remaining: -1 → skip
                   leaf 2: [1,9,2], remaining: 0 → save copy
                   leaf 7: [1,9,7], remaining: -4 → skip
```

Saving a copy (`path[:]`) is essential — if the original list were saved directly, later backtracking mutations would corrupt it.

### Algorithm

1. If the node is null, return.
2. Append `node.val` to the current path and subtract it from the remaining target.
3. If the node is a leaf and the remaining target is zero, append a copy of the path to results.
4. Otherwise, recurse into the left and right children.
5. Remove the current node from the path before returning (backtrack).

### Complexity analysis

- Time complexity: $O(n^2)$ — every node is visited once ($O(n)$), and copying a path at a leaf takes $O(h)$ time. In the worst case (all root-to-leaf paths are valid), the total copy work is $O(n \cdot h)$, which is $O(n^2)$ for a balanced tree and $O(n^2)$ for a skewed tree.
- Space complexity: $O(n \cdot h)$ — the output stores one copy per matching path, and the call stack uses $O(h)$. In the worst case both are $O(n^2)$.

```python
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def findPaths(self, root: Optional[TreeNode], target_sum: int) -> List[List[int]]:
        result: List[List[int]] = []
        self._dfs(root, target_sum, [], result)
        return result

    def _dfs(
        self,
        node: Optional[TreeNode],
        remaining: int,
        path: List[int],
        result: List[List[int]],
    ) -> None:
        if node is None:
            return

        path.append(node.val)
        remaining -= node.val

        if node.left is None and node.right is None and remaining == 0:
            result.append(path[:])
        else:
            self._dfs(node.left, remaining, path, result)
            self._dfs(node.right, remaining, path, result)

        path.pop()
```

## Solution 2: Iterative DFS

Each stack frame carries `(node, target, path_so_far)` where `path_so_far` is an immutable snapshot — built with `path + [child.val]` rather than a shared mutable list. This eliminates the need for explicit backtracking, at the cost of creating a new list at every node instead of mutating and undoing one.

### Complexity analysis

- Time complexity: $O(n^2)$ — $O(n)$ nodes visited and each push copies the current path in $O(h)$ time.
- Space complexity: $O(n \cdot h)$ — the stack can hold $O(n)$ frames simultaneously in a wide tree, each carrying an $O(h)$-length path. Same asymptotic bound as the recursive version.

```python
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def findPaths(self, root: Optional[TreeNode], target_sum: int) -> List[List[int]]:
        result: List[List[int]] = []
        if root is None:
            return result

        stack = [(root, target_sum, [])]
        while stack:
            node, target, path = stack.pop()
            curr_path = path + [node.val]
            curr_remaining = target - node.val

            if node.left is None and node.right is None and curr_remaining == 0:
                result.append(curr_path)
    
            if node.right:
                stack.append((node.right, curr_remaining, curr_path))
            if node.left:
                stack.append((node.left, curr_remaining, curr_path))
        return result
```
