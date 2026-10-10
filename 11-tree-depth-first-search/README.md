# Pattern: Tree Depth First Search

## Overview

Depth-first search (DFS) on a binary tree follows one branch as far as possible before backtracking and trying the next. The current recursive call stack represents the complete path from the root to the current node — that is the pattern's defining property and the reason it works so well for path-based problems.

BFS would also visit every node, but it visits them level by level. When an answer depends on what lies *along* a single branch — a sum, a sequence, a height — carrying that state through a queue is awkward. DFS carries it naturally through the call stack.

> Core idea: recurse into one child, then the other. Each call receives information from its parent and returns information to it. The recursive call stack is the current root-to-node path.

## The core pattern

Each node has three responsibilities:

1. **Receive** state from its parent (a remaining target, a running path, a number built so far).
2. **Decide** what to do at a leaf (record a result, return a count).
3. **Return** something to its parent (the best arm upward, a subtree count, a boolean).

```plaintext
        12
       /  \
      7    1      S = 23
     /    / \
    9    10  5

recurse(12, remaining=23):  remaining = 23 - 12 = 11  → recurse both children
  recurse(7, remaining=11):  remaining = 11 - 7 = 4   → recurse both children
    recurse(9, remaining=4):  remaining = 4 - 9 = -5  → leaf, -5 ≠ 0 → false
  recurse(1, remaining=11):  remaining = 11 - 1 = 10  → recurse both children
    recurse(10, remaining=10): remaining = 10 - 10 = 0 → leaf, 0 == 0 → true ✓
    recurse(5, remaining=10):  remaining = 10 - 5 = 5  → leaf, 5 ≠ 0  → false
```

## Template

```plaintext
function dfs(node, state_from_parent):
    if node is null:
        return base_value

    updated_state = update(state_from_parent, node.val)

    if node is a leaf:
        return leaf_result(updated_state)

    left  = dfs(node.left,  updated_state)
    right = dfs(node.right, updated_state)
    return combine(left, right)
```

Three things change between problems:

1. What `state_from_parent` carries down (a remaining target, a running number, a path list, a prefix sum).
2. What the leaf case produces (a boolean, a saved path copy, the number itself, or nothing at all).
3. How the two child results combine (OR for a boolean, + for a count, max for a best value).

## Iterative template

CPython's default call-stack depth is 1 000 frames. A balanced tree of 20 levels fits easily, but a skewed tree with 10 000 nodes creates 10 000 nested frames and raises `RecursionError`. The fix is to maintain the stack yourself — all state that lived in recursive parameters moves into `(node, state)` tuples.

```plaintext
function iterative_dfs(root, initial_state):
    if root is None:
        return base_value

    stack ← [(root, initial_state)]
    result ← base_value

    while stack is not empty:
        node, state = stack.pop()
        updated_state = update(state, node.val)

        if node is a leaf:
            result = merge(result, leaf_result(updated_state))
        else:
            # push right first so left is popped (visited) first
            if node.right is not None:
                stack.push((node.right, updated_state))
            if node.left is not None:
                stack.push((node.left, updated_state))

    return result
```

Three differences from the recursive version:

1. **State travels in the tuple**, not in the call frame. Every piece of information the recursive version received as a parameter must be bundled into the `(node, state)` pair.
2. **Push right before left** so the left child is popped and visited first — matching recursive pre-order (left before right).
3. **No automatic backtracking.** The recursive version undoes path mutations on the way back up. The iterative version has no return path; when a problem needs a growing path list, carry an immutable snapshot in each tuple (`path + [node.val]`) instead of a shared mutable list with `append` / `pop`.

When to use: any time the tree could have more nodes in a single root-to-leaf path than Python's recursion limit. Practical triggers: heavily skewed trees, contest inputs with $n$ up to $10^5$, or problems that explicitly mention deeply nested structures.

## Variations

### 1. Pass a remaining target down, return a boolean

Subtract each node's value from the remaining target. At a leaf, check if the remainder is zero.

Problems:

- [Binary Tree Path Sum](./01-binary-tree-path-sum.md)
- [Path With Given Sequence](./04-path-with-given-sequence.md)

### 2. Collect matching paths

Maintain a mutable path list. Append the current node on the way in, save a copy at matching leaves, remove the current node on the way out (backtracking).

Problems:

- [All Paths for a Sum](./02-all-paths-for-a-sum.md)

### 3. Build a value as you descend

Pass a running value down (a number formed from digits, a prefix sum). No backtracking is needed — the value is passed by copy.

Problems:

- [Sum of Path Numbers](./03-sum-of-path-numbers.md)
- [Count Paths for a Sum](./05-count-paths-for-a-sum.md)

### 4. Combine both subtrees at each node

Each child returns its best single arm upward. The current node joins both arms to form the best complete path through itself, updates a global answer, and returns only the better arm to its parent. A path can bend only once.

Problems:

- [Tree Diameter](./06-tree-diameter.md)
- [Path with Maximum Sum](./07-path-with-maximum-sum.md)

The key insight for this variant: the **global answer** (best complete path through the current node) and the **return value** (best arm for the parent to extend) are different things. Confusing them is the most common mistake.

## Recognize it when

- The problem asks about a **path** or **branch** in the tree.
- The path starts at the root or ends at a leaf.
- You need a sum, count, sequence, or collected list along a root-to-leaf path.
- A node's answer depends on values in its subtrees (height, diameter, max sum).
- You must build or check all root-to-leaf paths.

## Reach for something else when

- The answer is grouped by depth or per level: use Tree Level Order Traversal.
- You need the nearest node or minimum depth: BFS stops as soon as it finds the first matching level.
- The tree can be extremely deep (tens of thousands of nodes in a skewed chain): recursion may overflow so use an iterative DFS with an explicit stack.
- The data is a graph with cycles: DFS works but you also need a `visited` set.

## Pitfalls

- Treating a one-child node as a leaf: a leaf has *no* children. Check `node.left is None and node.right is None`.
- Forgetting to backtrack: every `path.append(node.val)` must be paired with `path.pop()` before returning. Missing it contaminates every later path.
- Saving the list object instead of a copy: `result.append(path)` saves a reference and later backtracking mutates it. Always `result.append(path[:])`.
- Stopping early when values can be negative: a negative node after a positive one can bring the sum back into range. Never prune on `remaining < 0` alone.
- Confusing the path returned upward with the best complete path: in diameter and maximum-sum problems, the arm returned to the parent uses only one child while the global candidate uses both.
- Seeding the global maximum with 0: node values can be negative. Use `float("-inf")`.

## Key takeaways

- DFS follows one branch before another; the call stack is the current path.
- Decide what information flows *down* and what result flows *back up*: they are often different.
- Visit each node once: $O(n)$ time, $O(h)$ call-stack space.
- Undo every path mutation before returning to the parent.
- When combining both subtrees (diameter, max-sum), the global update and the return value serve different roles.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
