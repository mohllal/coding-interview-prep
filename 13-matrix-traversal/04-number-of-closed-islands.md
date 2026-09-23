---
title: Number of Closed Islands
difficulty: Medium
leetcode: https://leetcode.com/problems/number-of-closed-islands/
tags:
  - Array
  - Depth-First Search
  - Breadth-First Search
  - Union-Find
  - Matrix
---

# Number of Closed Islands

## Problem description

Given a 2D matrix containing only `1`s (land) and `0`s (water), count the closed islands.

An island is a connected set of `1`s surrounded by either an edge of the matrix or `0`s. Cells are connected horizontally and vertically, not diagonally.

A closed island is one that is surrounded entirely by water. By definition it cannot touch the edge of the matrix, since a cell on the edge has no water beyond it.

## Examples

**Example 1:**

```plaintext
Input: matrix = [[0, 0, 0, 0, 0, 0],
                 [0, 1, 1, 0, 0, 0],
                 [0, 1, 1, 0, 0, 0],
                 [0, 0, 0, 0, 0, 1],
                 [0, 0, 0, 0, 0, 0]]
Output: 1
Explanation: Two islands. The 2x2 block is ringed by water, so it counts.
             The single cell at (3, 5) sits on the right edge, so it does not.

0 0 0 0 0 0
0 1 1 0 0 0
0 1 1 0 0 0
0 0 0 0 0 1   <- touches the right edge
0 0 0 0 0 0
```

**Example 2:**

```plaintext
Input: matrix = [[0, 0, 0, 0, 0, 0],
                 [0, 1, 1, 0, 0, 0],
                 [0, 1, 1, 0, 0, 0],
                 [0, 0, 0, 0, 1, 0],
                 [0, 0, 0, 0, 0, 0]]
Output: 2
Explanation: Both islands are surrounded by water on every side.

0 0 0 0 0 0
0 1 1 0 0 0
0 1 1 0 0 0
0 0 0 0 1 0
0 0 0 0 0 0
```

## Constraints

- `1 <= len(matrix), len(matrix[0]) <= 100`
- `matrix[i][j]` is `0` or `1`

## Hints

<details>
<summary>Hint 1</summary>

An island fails the test the moment any one of its cells sits on the border. Rather than checking that at the end, consider removing the disqualified islands before you start counting — what happens if you flood inward from every border cell first?

</details>

<details>
<summary>Hint 2</summary>

Alternatively, keep one pass and have the depth-first search report whether it ever stepped onto a border cell. Be careful combining those reports: `closed = closed and dfs(...)` will skip the recursive call entirely once `closed` turns false, leaving part of the island unvisited.

</details>

## Solution 1: Sink the border-connected land first

### Intuition

An island is disqualified if any one of its cells touches the border. Instead of tracking that during the count, remove those islands from the grid entirely and then count whatever survives — which is exactly [Number of Islands](./01-number-of-islands.md) on the reduced grid.

Flooding inward from every border cell sinks precisely the land that can reach the edge. Whatever land remains afterwards is unreachable from the border, which is the definition of closed:

```plaintext
0 0 0 0 0 0          0 0 0 0 0 0
0 1 1 0 0 0          0 1 1 0 0 0
0 1 1 0 0 0   -->    0 1 1 0 0 0      then count islands: 1
0 0 0 0 0 1          0 0 0 0 0 0
0 0 0 0 0 0          0 0 0 0 0 0
        ^                    ^
  border land           sunk by the border pass
```

The appeal is that neither half has to know about the other. The first pass only removes land, the second only counts it, and both are floods you already know how to write.

### Algorithm

1. Flood from every cell in the first and last column, and every cell in the first and last row, sinking any land reached
2. Scan the grid; each remaining land cell that has not been claimed starts a closed island
3. Increment the count and flood from it to claim the rest
4. Return the count

### Complexity analysis

- Time complexity: $O(m \times n)$ — two passes, each entering every cell at most once
- Space complexity: $O(m \times n)$ — the recursion stack when the grid is mostly land

```python
from typing import List


class Solution:
    def closed_island(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])

        def dfs(r: int, c: int) -> None:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return

            if matrix[r][c] == 0:
                return

            matrix[r][c] = 0
            for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                dfs(r + dr, c + dc)

        for r in range(rows):
            dfs(r, 0)
            dfs(r, cols - 1)

        for c in range(cols):
            dfs(0, c)
            dfs(rows - 1, c)

        closed = 0
        for r in range(1, rows - 1):
            for c in range(1, cols - 1):
                if matrix[r][c] == 1:
                    closed += 1
                    dfs(r, c)

        return closed
```

## Solution 2: Let the flood report back

### Intuition

The same answer in one pass, by making the flood return whether the island it sank ever touched the border — the same upgrade that turned [Number of Islands](./01-number-of-islands.md) into [Biggest Island](./02-biggest-island.md), carrying a boolean instead of a count.

A cell is a failure if it is on the border. An island is closed only if none of its cells failed, so each call combines its own verdict with those of its four neighbours.

The trap is in that combination. Writing it as `closed = closed and dfs(...)` reads naturally but is wrong: once `closed` is false, Python short-circuits and never calls `dfs`, so the rest of the island is left unvisited and the outer scan counts its leftovers as fresh islands. Evaluate the recursion first and combine afterwards.

### Algorithm

1. Scan every cell; skip water
2. On land, flood from it and increment the count if the flood reports no border contact
3. The flood returns `False` when the cell is out of bounds, since that means the caller was on the border
4. It returns `True` when the cell is water, which blocks the spread without disqualifying anything
5. Otherwise it sinks the cell, evaluates all four neighbours, and returns whether every one of them reported `True`
6. Return the count

### Complexity analysis

- Time complexity: $O(m \times n)$ — one scan, with all floods combined entering each cell at most once
- Space complexity: $O(m \times n)$ — the recursion stack when the grid is mostly land

```python
from typing import List


class Solution:
    def closed_island(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])

        def dfs(r: int, c: int) -> bool:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return False            # fell off the grid: the caller was on the border

            if matrix[r][c] == 0:
                return True             # water stops the spread without disqualifying

            matrix[r][c] = 0
            # every neighbour must be explored, so collect the results before combining
            results = [
                dfs(r + dr, c + dc)
                for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1))
            ]
            return all(results)

        closed = 0
        for r in range(rows):
            for c in range(cols):
                if matrix[r][c] == 1 and dfs(r, c):
                    closed += 1

        return closed
```
