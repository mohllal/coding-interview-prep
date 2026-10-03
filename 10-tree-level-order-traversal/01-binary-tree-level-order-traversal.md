---
title: Binary Tree Level Order Traversal
difficulty: Medium
leetcode: https://leetcode.com/problems/binary-tree-level-order-traversal/
tags:
  - Tree
  - Breadth-First Search
  - Binary Tree
---

# Binary Tree Level Order Traversal

## Problem description

Given a binary tree, populate an array to represent its level-by-level traversal. Put the values of all nodes of each level, from left to right, in a separate sub-array.

## Examples

**Example 1:**

```plaintext
Input: root = [1, 2, 3, 4, 5, 6, 7]

        1
      /   \
     2     3
    / \   / \
   4   5 6   7

Output: [[1], [2, 3], [4, 5, 6, 7]]
```

**Example 2:**

```plaintext
Input: root = [12, 7, 1, 9, null, 10, 5]

        12
       /  \
      7    1
     /    / \
    9    10  5

Output: [[12], [7, 1], [9, 10, 5]]
```

## Constraints

- The number of nodes in the tree is in the range `[0, 2000]`.
- `-1000 <= Node.val <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

A queue hands nodes back in the order you added them. If you add children left to right, what order do you get them back in?

</details>

<details>
<summary>Hint 2</summary>

The queue holds nodes from at most two levels at once. Before processing a level, its size is exactly the number of nodes on that level — record it.

</details>

## Solution

### Intuition

A queue naturally visits a tree level by level: dequeue a node, enqueue its children, and the children wait behind every node still left on the current level.

The only extra trick is knowing where one level ends and the next begins. At the start of each round, the queue contains **exactly** the nodes of one level, so `len(queue)` is that level's size. Process that many nodes — collecting their values and enqueuing their children — and the queue is left holding exactly the next level.

```plaintext
round 1   queue: [1]            size=1   level: [1]
          enqueue 2, 3

round 2   queue: [2, 3]         size=2   level: [2, 3]
          enqueue 4, 5, 6, 7

round 3   queue: [4, 5, 6, 7]   size=4   level: [4, 5, 6, 7]
          no children

queue empty → [[1], [2, 3], [4, 5, 6, 7]]
```

### Algorithm

1. If the root is empty, return `[]`.
2. Put the root in a queue.
3. While the queue is not empty:
   - Record `level_size = len(queue)`.
   - Pop `level_size` nodes, recording each value and enqueuing its non-null children left then right.
   - Append the recorded values as one level.
4. Return the list of levels.

### Complexity analysis

- Time complexity: $O(n)$ — every node is enqueued and dequeued once.
- Space complexity: $O(n)$ — the output holds every value, and the queue can hold a whole level, which is up to $n/2$ nodes in the last level of a full tree.

Where the $n/2$ comes from: every node has at most two children, so each level holds at most **twice** as many nodes as the level above it. In a perfect tree every level hits that limit and doubles:

```plaintext
level 0                o                1 node
                    /     \
level 1           o         o           2 nodes
                 / \       / \
level 2         o   o     o   o         4 nodes
               / \ / \   / \ / \
level 3       o  o o  o o  o o  o       8 nodes
                                       ---------
                                       15 nodes total

1 + 2 + 4 = 7 nodes above the last level
                8 nodes in the last level  →  8 = (15 + 1) / 2
```

Doubling makes each level one node bigger than **all the levels above it combined** ($2^k = (1 + 2 + \dots + 2^{k-1}) + 1$). So the last level holds $(n + 1) / 2$ of the $n$ nodes — about half the tree.

No binary tree can do better. Take any level with `w` nodes and count the minimum number of nodes the tree must have:

1. The level itself contributes `w` nodes.
2. The levels above it: each parent has at most two children, so `w` nodes need at least `w / 2` parents. Those need at least `w / 4` parents of their own, and so on until a single root:

   ```plaintext
   w = 8:   4 + 2 + 1 = 7 = w - 1
   ```

   When `w` is a power of two, $w/2 + w/4 + \dots + 1 = w - 1$ exactly. For any other `w`, the parent counts round up (5 nodes need 3 parents, not 2.5), which only makes the total larger. Either way, at least `w - 1` total nodes sit above the level.
3. Add them up:

   $$n \ge w + (w - 1) = 2w - 1$$

4. Solve for `w`:

   $$2w - 1 \le n \implies w \le \frac{n + 1}{2}$$

So no level of any binary tree with `n` nodes is ever wider than $(n + 1) / 2$ — roughly $n/2$. The perfect tree above is exactly the case where that bound is reached.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


class Solution:
    def level_order(self, root: Optional[TreeNode]) -> List[List[int]]:
        if not root:
            return []

        result = []
        queue = deque([root])

        while queue:
            level = []
            for _ in range(len(queue)):
                node = queue.popleft()
                level.append(node.val)
                if node.left:
                    queue.append(node.left)
                if node.right:
                    queue.append(node.right)
            result.append(level)

        return result
```
