---
title: Even Odd Tree
difficulty: Medium
leetcode: https://leetcode.com/problems/even-odd-tree/
tags:
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Even Odd Tree

## Problem description

Given a binary tree, return `true` if it is an Even-Odd tree, otherwise return `false`. Levels are indexed from `0`, and the tree must satisfy two rules:

- At every **even-indexed** level, all values are **odd** and strictly **increasing** from left to right.
- At every **odd-indexed** level, all values are **even** and strictly **decreasing** from left to right.

## Examples

**Example 1:**

```plaintext
Input:
      1           level 0: [1]       odd, increasing  ✓
     / \
   10   4         level 1: [10, 4]   even, decreasing ✓
   / \
  3   7           level 2: [3, 7]    odd, increasing  ✓

Output: true
```

**Example 2:**

```plaintext
Input:
      5           level 0: [5]       odd, increasing  ✓
     / \
    9   3         level 1: [9, 3]    must be even     ✗
   /     \
  12      8

Output: false
Explanation: Level 1 holds odd values 9 and 3, but odd-indexed levels must hold even values.
```

**Example 3:**

```plaintext
Input:
      7           level 0: [7]       odd, increasing  ✓
     / \
   10   2         level 1: [10, 2]   even, decreasing ✓
   / \
  12  8           level 2: [12, 8]   must be odd      ✗

Output: false
Explanation: Level 2 is even-indexed, so its values must be odd, but 12 and 8 are even.
```

## Constraints

- The number of nodes in the tree is in the range `[1, 10^5]`.
- `1 <= Node.val <= 10^6`

## Hints

<details>
<summary>Hint 1</summary>

Every rule only compares a node with the node immediately to its left on the same level. A level-by-level BFS visits exactly those pairs in order.

</details>

<details>
<summary>Hint 2</summary>

Track the previous value on the current level. Seed it with `-inf` on increasing levels and `+inf` on decreasing ones, so the first node on each level always passes the ordering check.

</details>

## Solution

### Intuition

Both rules are local: a node only needs to be compared with its left neighbour on the same level, plus a parity check on its own value. A level-by-level BFS dequeues each level left to right, so keep the previous value and check every node as it comes off the queue.

Seeding `previous` with `-inf` on even levels and `+inf` on odd levels means the first node of a level never fails the ordering check, so there's no special case for it.

```plaintext
level 0 (even): odd + increasing,  previous = -inf
  1   odd ✓   1 > -inf ✓

level 1 (odd): even + decreasing,  previous = +inf
  10  even ✓  10 < +inf ✓
  4   even ✓  4 < 10   ✓

level 2 (even): odd + increasing,  previous = -inf
  3   odd ✓   3 > -inf ✓
  7   odd ✓   7 > 3    ✓

→ true
```

### Algorithm

1. Start a BFS from the root with `level = 0`.
2. For each level, set `previous = -inf` if the level is even, otherwise `+inf`.
3. For each node dequeued on the level:
   - Even level: return `false` if the value is even or `<= previous`.
   - Odd level: return `false` if the value is odd or `>= previous`.
   - Set `previous` to the value and enqueue the children.
4. Increment `level` after each level. If the queue empties without a violation, return `true`.

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
    def is_even_odd_tree(self, root: Optional[TreeNode]) -> bool:
        queue = deque([root])
        level = 0

        while queue:
            even_level = level % 2 == 0
            previous = float("-inf") if even_level else float("inf")

            for _ in range(len(queue)):
                node = queue.popleft()

                if even_level and (node.val % 2 == 0 or node.val <= previous):
                    return False
  
                if not even_level and (node.val % 2 == 1 or node.val >= previous):
                    return False

                previous = node.val

                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)

            level += 1

        return True
```
