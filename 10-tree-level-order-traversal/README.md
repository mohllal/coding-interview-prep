# Pattern: Tree Level Order Traversal

## Overview

Level order traversal — breadth-first search (BFS) on a tree — visits nodes one level at a time: the root, then all of its children, then all of their children, and so on, each level from left to right.

Its defining property is the visiting order: no node is visited before a node that is closer to the root. That makes it the natural tool whenever an answer depends on a node's **depth** rather than on its ancestors or descendants — something computed over all the nodes at one depth, a relationship between nodes at the same depth, or the shallowest node that satisfies a condition.

Depth-first traversal can solve many of these too, but it has to carry the depth around and stitch results back together. BFS hands you each level as a unit.

> Core idea: process the tree in waves, one level per wave. A FIFO queue keeps every node of the current level ahead of every node of the next, and the queue's length at the start of a wave is exactly the size of that level.

## The core pattern

At the start of every round the queue holds **exactly one level**. Its length is that level's size. Dequeue that many nodes, enqueuing their children as you go, and the queue is left holding exactly the next level.

```plaintext
        12
       /  \
      7    1
     /    / \
    9    10  5

round   queue at start   level_size   dequeued       enqueued
-----   --------------   ----------   ------------   ----------
  1     [12]                  1       12             7, 1
  2     [7, 1]                2       7, 1           9, 10, 5
  3     [9, 10, 5]            3       9, 10, 5       —

levels: [12], [7, 1], [9, 10, 5]
```

Without recording the queue size at the start, the children you just enqueued would mix into the current level. Fixing `level_size` before the inner loop is what draws the line between levels.

## Template

```plaintext
if root is empty:
    return empty result

queue ← [root]
while queue is not empty:
    level_size ← length of queue          # record before the level starts
    start_level()                         # reset any per-level state
    for i in 0 .. level_size - 1:
        node ← dequeue
        visit(node, i)
        if node.left:  enqueue node.left
        if node.right: enqueue node.right
    finish_level()                        # record, compare or reduce the level
```

Problems differ only in three places:

1. What `start_level` resets: a list of values, a running sum, a maximum, a `previous` pointer.
2. What `visit` does with each node: record it, compare it with its left neighbour, link it, or stop early.
3. What `finish_level` produces: the whole level, a single number, or nothing at all.

## Variations

### 1. Collect, reduce or select per level

Keep the loop exactly as in the template and change only what each level turns into:

- The values themselves ([Binary Tree Level Order Traversal](./01-binary-tree-level-order-traversal.md))
- Their average ([Level Averages](./04-level-averages-in-a-binary-tree.md))
- Their maximum ([Largest Value in Each Row](./05-find-largest-value-in-each-tree-row.md))
- Running best across levels ([Maximum Level Sum](./06-maximum-level-sum-of-a-binary-tree.md)).

### 2. Change how levels are recorded, not how they're traversed

The queue order never changes and only the storing does:

- Pushing finished levels onto the front of a deque reverses their order ([Reverse Level Order](./02-reverse-level-order-traversal.md)).
- Appending a level's values to the front of a deque on alternate rounds reverses those rounds ([Zigzag](./03-zigzag-traversal.md)).

```plaintext
dequeued:       7, 1
left-to-right:  append      → [7, 1]
right-to-left:  appendleft  → [1, 7]
```

### 3. Look at neighbours within a level

Because each level comes off the queue left to right, a single `previous` variable, reset at the start of each level, gives you every adjacent pair.

- Use it to validate an ordering ([Even Odd Tree](./07-even-odd-tree.md)).
- Link siblings with `next` pointers ([Connect Level Order Siblings](./10-connect-level-order-siblings.md)).

### 4. Ignore level boundaries

Some problems only care about the overall BFS order. Then the inner `for` loop and the size recording disappear:

- Link every node to the one dequeued before it ([Connect All Level Order Siblings](./13-connect-all-level-order-siblings.md))
- Stop right after the key node ([Level Order Successor](./09-level-order-successor.md)).

### 5. Stop early

BFS reaches shallow nodes before deep ones, so the first node that satisfies a condition is also the shallowest one. The first leaf dequeued gives the minimum depth ([Minimum Depth](./08-minimum-depth-of-a-binary-tree.md)) — no need to explore the rest of the tree.

### 6. Carry extra state with each node

Enqueue `(node, position)` pairs when the answer depends on where a node would sit in a complete tree. With heap-style positions — children of `i` at `2i + 1` and `2i + 2` — a level's width is `last - first + 1` ([Maximum Width](./11-maximum-width-of-binary-tree.md)).

### 7. Any branching factor

Nothing in the loop depends on a node having exactly two children. Replace the `left`/`right` enqueues with a loop over the node's children and everything else stays the same ([N-ary Level Order](./12-n-ary-tree-level-order-traversal.md)).

## Recognize it when

The answer is grouped by depth: one result per level, or one value reduced from each level. The answer compares or connects nodes that share a depth, regardless of which subtree they belong to. The answer is the shallowest, nearest or first-reached node that satisfies a condition, where exploring deeper first would waste work. In each case what matters about a node is its distance from the root, not the path that leads to it.

## Reach for something else when

- The answer flows along root-to-leaf paths: path sums, path lists, the diameter. DFS carries the path state naturally and BFS would have to store a path with every queued node.
- You need the maximum depth or a subtree property: height, balance, subtree sums. A recursive DFS is shorter and uses $O(h)$ space instead of $O(w)$.
- The tree is very wide but shallow, and memory matters: BFS holds a whole level (up to $n/2$ nodes) while DFS holds only a path.
- The data is a graph, not a tree: the loop is the same, but you need a `visited` set, which trees don't.

## Pitfalls

- Reading `len(queue)` inside the inner loop: it grows as children are enqueued. Record it once, before the level starts.
- Forgetting the empty tree: `deque([None])` enqueues a `None`, and the first `.val` crashes. Return early when `root` is empty.
- Seeding a running max or best with `0`: node values can be negative. Start with `-inf`.
- Using `list.pop(0)` as a queue: it is $O(n)$ per pop. Use `collections.deque` and `popleft()`.
- Changing the enqueue order to change the output order: reordering how children enter the queue tangles every later level. Keep enqueuing left to right and change only how each level is recorded.
- Reasoning along paths when the question is about levels: nodes at the same depth can live in different subtrees, and a shorter subtree drops out of deeper levels. Work from what the queue holds for the level, not from where a single path leads.

## Key takeaways

- A queue plus a level-size recording splits a tree into its levels.
- Per-level problems differ only in what you reset, what you do per node, and what you produce per level.
- Nodes come off the queue left to right, so the previous node on a level is always the left neighbour.
- BFS finds the shallowest match first, so "minimum depth" style problems can stop early.
- Drop the size recording when level boundaries don't matter.

## Complexity

Every node is enqueued and dequeued exactly once, so traversal is $O(n)$ time.

The queue holds at most one level plus the children being added — up to about $n/2$ nodes for the last level of a full tree — so space is $O(w)$ for maximum width $w$, which is $O(n)$ in the worst case of a perfect or complete tree.

A skewed tree is the best case: every level holds one node, so the queue never holds more than one.

## Binary tree types and properties

### Terms

- Level: the root is level 0, its children level 1, and so on. A node's *depth* is its level.
- Height: the number of levels in the tree. A single node has height 1 and an empty tree has height 0.
- Leaf: a node with no children.
- Internal node: a node with at least one child.
- Width of a level: the number of nodes on it.

### Properties of every binary tree

These hold for any binary tree with $n$ nodes and height $h$:

- Level $i$ holds at most $2^i$ nodes, because each node above it has at most two children.
- A tree of height $h$ holds at most $1 + 2 + \dots + 2^{h-1} = 2^h - 1$ nodes.
- So the height is between $\lceil \log_2(n + 1) \rceil$ (packed as tightly as possible) and $n$ (a single chain).
- There are exactly $n - 1$ edges, one from each node to its parent, except for the root.
- If $L$ is the number of leaves and $D$ the number of nodes with two children, then $L = D + 1$.
- No level is wider than $(n + 1) / 2$

### Types

**Full** (also called *strict* or *proper*): every node has 0 or 2 children, never exactly 1.

```plaintext
      1
     / \
    2   3
       / \
      4   5
```

Since $L = D + 1$ and every internal node has two children, a full tree has one more leaf than internal nodes, and $n$ is always odd.

**Complete**: every level is completely filled except possibly the last, and the last level is filled from the left with no gaps.

```plaintext
        1
      /   \
     2     3
    / \   /
   4   5 6
```

A complete tree fits in an array with no holes: the node at index $i$ has children at $2i + 1$ and $2i + 2$ and its parent at $(i - 1) // 2$. This is why heaps are complete trees. Its height is $\lceil \log_2(n + 1) \rceil$, the minimum possible.

**Perfect**: every internal node has two children and every leaf is on the same level — a complete tree whose last level is full.

```plaintext
        1
      /   \
     2     3
    / \   / \
   4   5 6   7
```

With height $h$, it has exactly $n = 2^h - 1$ nodes and $2^{h-1} = (n + 1) / 2$ leaves. Every level is twice the one above it, so this is the shape where BFS's queue is largest.

**Balanced** (height-balanced): at every node, the heights of the left and right subtrees differ by at most 1.

```plaintext
        1
      /   \
     2     3
    / \     \
   4   5     6
  /
 7
```

Balance keeps the height at $O(\log n)$, which is what makes self-balancing search trees (AVL, red-black) fast. Perfect and complete trees are always balanced but a balanced tree need not be complete.

**Degenerate** (skewed): every node has at most one child, so the tree is effectively a linked list.

```plaintext
  1
   \
    2
     \
      3
       \
        4
```

The height is $n$, so the tree loses every $\log n$ advantage. For BFS it is the easiest shape — the queue holds one node at a time — but recursive DFS goes $n$ calls deep.

**Binary search tree (BST)**: for every node, all values in its left subtree are smaller and all values in its right subtree are larger.

```plaintext
        8
      /   \
     3     10
    / \      \
   1   6      14
```

An in-order traversal (left, node, right) visits the values in sorted order, and search, insert and delete cost $O(h)$ — $O(\log n)$ when balanced, $O(n)$ when degenerate. The BST rule is about values and it's independent of the shape types above.

## Building a tree from its array form

Problems usually write a tree as a list in level order, such as `root = [1, null, 2, 3]`. You receive an already-built tree, but you need the reverse conversion to test your own solution locally.

### The format

- Values are listed level by level, left to right — the same order BFS visits them.
- `null` marks a missing child.
- A `null` node has no children, so **nothing is listed for its children**. The next values belong to the next real node.
- Trailing `null`s are dropped.

The third rule is the one that is confusing a bit. It means the heap formula ($2i + 1$, $2i + 2$) does **not** work in general — it would only work if every `null` also had two `null` children listed.

```plaintext
root = [1, null, 2, 3]

level order format               heap indexing (wrong)

      1                                1
       \                              / \
        2                         null   2
       /                           /
      3                           3          ← index 3 = 2 * 1 + 1,
                                                 a child of the null
```

### The algorithm

The array is the BFS order of the tree, so rebuild it with a BFS and keep a queue of nodes waiting for children, and read the array two values at a time.

```plaintext
values = [1, 2, 3, null, 4, null, 5]

create root 1                            queue: [1]
parent 1:  left = 2,     right = 3       queue: [2, 3]
parent 2:  left = null,  right = 4       queue: [3, 4]
parent 3:  left = null,  right = 5       queue: [4, 5]
parent 4:  values exhausted → done

      1
     / \
    2   3
     \   \
      4   5
```

Only real nodes enter the queue, so a `null` never claims any values — which is exactly the third rule of the format.

```python
from collections import deque
from typing import List, Optional


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def build_tree(values: List[Optional[int]]) -> Optional[TreeNode]:
    if not values or values[0] is None:
        return None

    root = TreeNode(values[0])
    queue = deque([root])
    i = 1

    while queue and i < len(values):
        parent = queue.popleft()

        if values[i] is not None:
            parent.left = TreeNode(values[i])
            queue.append(parent.left)
        i += 1

        if i < len(values) and values[i] is not None:
            parent.right = TreeNode(values[i])
            queue.append(parent.right)
        i += 1

    return root
```

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
