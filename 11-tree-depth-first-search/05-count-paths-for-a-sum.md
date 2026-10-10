---
title: Count Paths for a Sum
difficulty: Medium
leetcode_title: Path Sum III
leetcode: https://leetcode.com/problems/path-sum-iii/
tags:
  - Tree
  - Depth-First Search
  - Binary Tree
  - Prefix Sum
---

# Count Paths for a Sum

## Problem description

Given a binary tree and a number `S`, count all paths in the tree whose node values sum to `S`. Paths may start and end at any node, but must follow the parent-to-child direction (top to bottom).

## Examples

**Example 1:**

```plaintext
Input: root = [1, 7, 9, 6, 5, 2, 3], S = 12

        1
       / \
      7   9
     / \ / \
    6  5 2  3

Output: 3
Explanation: paths are [7,5], [1,2,9], [9,3]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 3, 14, 10, 5, null, null, null, null, null, null, null, 11], S = 11

         12
        /  \
       7    1
      / \  / \
     3  14 10  5
              \
              11

Output: 2
Explanation: paths are [11] and [10, 1] — wait, checking: 10+1=11 yes and standalone 11 yes.
```

## Constraints

- The number of nodes in the tree is in the range `[0, 1000]`.
- `-10^9 <= Node.val <= 10^9`
- `-1000 <= targetSum <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Any path from node `a` to node `b` (where `a` is an ancestor of `b`) can be represented as the prefix sum at `b` minus the prefix sum just before `a`. What data structure lets you check how many earlier prefix sums differ from the current one by exactly `S`?

</details>

<details>
<summary>Hint 2</summary>

A hash map from prefix sum to frequency lets you answer that query in $O(1)$. Seed it with `{0: 1}` to account for paths that start at the root. After finishing a subtree, undo the current node's contribution so sibling branches are not affected.

</details>

## Solution

### Intuition

A path from any ancestor to the current node is a contiguous segment of a root-to-current path. If `prefix_sum` is the sum from the root to the current node, then any sub-path ending here with sum `S` corresponds to an ancestor whose prefix sum equals `prefix_sum - S`.

A hash map `count[prefix_sum]` tracks how many times each prefix sum has appeared along the current root-to-node path. At each node, `count[prefix_sum - S]` gives the number of valid paths ending at that node.

After processing both children, decrement `count[prefix_sum]` to remove the current node's contribution before returning to the parent.

```plaintext
S = 12, root = [1, 7, 9, 6, 5, 2, 3]

count = {0: 1}           prefix_sum = 0

  node 1: prefix_sum=1,  count[1-12]=-11 → 0 paths   count={0:1, 1:1}
    node 7: prefix_sum=8,  count[8-12]=-4 → 0 paths   count={..., 8:1}
      node 6: prefix_sum=14, count[14-12]=2 → count[2]=0 paths   ...
      node 5: prefix_sum=13, count[13-12]=1 → count[1]=1 path ✓  [7,5]
    node 9: prefix_sum=10, count[10-12]=-2 → 0 paths   count={..., 10:1}
      node 2: prefix_sum=12, count[12-12]=0 → count[0]=1 path ✓  [1,2,9] wait: 1+9+2=12 ✓
      node 3: prefix_sum=13, count[13-12]=1 → count[1]=1 path ✓  [9,3]? 9+3=12 ✓

total: 3
```

### Algorithm

1. Start with `count = {0: 1}` and `prefix_sum = 0`.
2. At each node, add `node.val` to `prefix_sum`.
3. Add `count[prefix_sum - S]` to the total (defaulting to 0).
4. Increment `count[prefix_sum]`.
5. Recurse into left and right children.
6. Decrement `count[prefix_sum]` before returning.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited once and each hash map operation is $O(1)$.
- Space complexity: $O(n)$ — the hash map holds at most one entry per node on the current root-to-leaf path, and the call stack adds $O(h)$.

```python
from collections import defaultdict
from typing import Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def countPaths(self, root: Optional[TreeNode], target_sum: int) -> int:
        count: defaultdict[int, int] = defaultdict(int)
        count[0] = 1
        return self._dfs(root, target_sum, 0, count)

    def _dfs(
        self,
        node: Optional[TreeNode],
        target_sum: int,
        prefix_sum: int,
        count: defaultdict,
    ) -> int:
        if node is None:
            return 0

        prefix_sum += node.val
        remaining = prefix_sum - target_sum
  
        paths = count[remaining]
        count[prefix_sum] += 1

        paths += self._dfs(node.left, target_sum, prefix_sum, count)
        paths += self._dfs(node.right, target_sum, prefix_sum, count)

        count[prefix_sum] -= 1
        return paths
```
