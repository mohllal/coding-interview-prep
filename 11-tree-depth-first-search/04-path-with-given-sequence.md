---
title: Path With Given Sequence
difficulty: Medium
tags:
  - Tree
  - Depth-First Search
  - Binary Tree
---

# Path With Given Sequence

## Problem description

Given a binary tree and a number sequence, determine whether the sequence appears as a root-to-leaf path in the tree.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 7, 9, null, null, 2, 9], sequence = [1, 9, 9]

        1
       / \
      7   9
         / \
        2   9

Output: true
Explanation: 1 -> 9 -> 9 is a valid root-to-leaf path.
```

**Example 2:**

```plaintext
Input: root = [1, 0, 1, null, null, 6, 5], sequence = [1, 0, 7]

        1
       / \
      0   1
         / \
        6   5

Output: false
Explanation: No root-to-leaf path matches [1, 0, 7].
```

**Example 3:**

```plaintext
Input: root = [1, 7, 9, null, null, 2, 9], sequence = [1, 9]

Output: false
Explanation: [1, 9] ends at a non-leaf node, so it is not a valid path.
```

## Constraints

- The number of nodes in the tree is in the range `[1, 5000]`.
- `-10^8 <= Node.val <= 10^8`
- `1 <= sequence.length <= 5000`

## Hints

<details>
<summary>Hint 1</summary>

Track an index into the sequence as you descend. At each node, check that `node.val` matches `sequence[index]`. What are the two base cases that terminate the recursion?

</details>

<details>
<summary>Hint 2</summary>

A path must end at a leaf. Even if all values match, arriving at a null node or finishing the sequence at a non-leaf node is not a valid match.

</details>

## Solution

### Intuition

Walk the sequence index in lockstep with the tree depth. At each node, verify the current value matches `sequence[index]`. Two conditions end the search:

- The value does not match → the current branch cannot hold the sequence.
- The sequence is exhausted at a leaf → a complete match is found.

A mismatch at a null node (the sequence runs past the end of the tree) or finishing the sequence at an internal node (the path does not end at a leaf) are both failures.

```plaintext
sequence = [1, 9, 9]

        1  idx=0  1==1 ✓
       / \
      7   9  idx=1  9==9 ✓
         / \
        2   9  idx=2  9==9 ✓, leaf → true
```

### Algorithm

1. If the node is null or `node.val != sequence[index]`, return `false`.
2. If the sequence is exhausted (`index == len(sequence) - 1`) and the node is a leaf, return `true`.
3. Recurse into left and right children with `index + 1`; return `true` if either succeeds.

### Complexity analysis

- Time complexity: $O(n)$ — every node is visited at most once.
- Space complexity: $O(h)$ — the call stack holds one path at a time.

```python
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def findPath(self, root: Optional[TreeNode], sequence: List[int]) -> bool:
        return self._dfs(root, sequence, 0)

    def _dfs(self, node: Optional[TreeNode], sequence: List[int], index: int) -> bool:
        # Check if we have run out of nodes or run past the end of the sequence
        if node is None or index >= len(sequence):
            return False

        # Check if the current node's value does not match the current sequence element
        if node.val != sequence[index]:
            return False

        # If we have reached end of sequence and we are at a leaf node, the whole sequence matches a root-to-leaf path
        if node.left is None and node.right is None and index == len(sequence) - 1:
            return True

        return self._dfs(node.left, sequence, index + 1) or self._dfs(node.right, sequence, index + 1)
```
