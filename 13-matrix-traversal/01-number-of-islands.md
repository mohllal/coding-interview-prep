---
title: Number of Islands
difficulty: Medium
leetcode: https://leetcode.com/problems/number-of-islands/
tags:
  - Array
  - Depth-First Search
  - Breadth-First Search
  - Union-Find
  - Matrix
---

# Number of Islands

## Problem description

Given a 2D matrix containing only `1`s (land) and `0`s (water), count the number of islands in it.

An island is a connected set of `1`s surrounded by either an edge of the matrix or `0`s. Cells are connected horizontally and vertically, not diagonally.

## Examples

**Example 1:**

```plaintext
Input: matrix = [[1, 1, 0, 0, 0],
                 [1, 1, 0, 0, 0],
                 [0, 0, 1, 0, 0],
                 [0, 0, 0, 1, 1]]
Output: 3
Explanation: The 2x2 block in the top-left, the single cell at (2, 2),
             and the pair at (3, 3) and (3, 4).

1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1
```

**Example 2:**

```plaintext
Input: matrix = [[1, 1, 1, 1, 0],
                 [1, 1, 0, 1, 0],
                 [1, 1, 0, 0, 0],
                 [0, 0, 0, 0, 0]]
Output: 1
Explanation: Every land cell reaches every other one through the top row.

1 1 1 1 0
1 1 0 1 0
1 1 0 0 0
0 0 0 0 0
```

## Constraints

- `m == len(matrix)`
- `n == len(matrix[i])`
- `1 <= m, n <= 300`
- `matrix[i][j]` is `0` or `1`

## Hints

<details>
<summary>Hint 1</summary>

Every land cell belongs to exactly one island. If you could mark every cell of an island the moment you find any one of its cells, then scanning the grid would meet each island exactly once — at whichever of its cells you happen to reach first.

</details>

<details>
<summary>Hint 2</summary>

From a land cell, step to its four neighbours, then to theirs, marking as you go. Mark a cell *before* you recurse into its neighbours, not after, or two adjacent cells will each step into the other forever.

</details>

## Solution 1: Recursive depth-first search

### Intuition

Every land cell belongs to exactly one island, so the count of islands equals the count of cells that were the *first* of their island to be seen.

Scan the grid in order. When you reach a land cell that has not yet been claimed, it must belong to an island nobody has counted, because any earlier cell of that island would have claimed this one already. Increment the counter, then run a depth-first search outward from it, marking every connected land cell so that the scan skips them all.

```plaintext
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1

(0,0) land, unclaimed  ->  count = 1, DFS sinks (0,0) (0,1) (1,0) (1,1)
(0,1) already water        skip           (the DFS overwrote it)
(1,0) already water        skip
(1,1) already water        skip
(2,2) land, unclaimed  ->  count = 2, DFS sinks (2,2)
(3,3) land, unclaimed  ->  count = 3, DFS sinks (3,3) (3,4)

answer = 3
```

Sinking the island — writing `0` over each visited cell — doubles as the visited marker, which avoids allocating a separate `visited` grid. It destroys the input, so make a copy first if the caller still needs it.

### Algorithm

1. Walk every cell of the grid in row-major order
2. Skip any cell that is water
3. On land, increment the island count and run a depth-first search from that cell
4. The depth-first search returns immediately if the cell is out of bounds or is water
5. Otherwise it sets the cell to `0` and recurses into all four neighbours
6. Return the count once the scan finishes

### Complexity analysis

- Time complexity: $O(m \times n)$ — the outer scan inspects each cell once, and because sunk cells are never land again, all the searches combined enter each cell at most once
- Space complexity: $O(m \times n)$ — the recursion stack in the worst case of a grid that is entirely land; no auxiliary grid is allocated

```python
from typing import List


class Solution:
    def num_islands(self, matrix: List[List[int]]) -> int:
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

        islands = 0
        for r in range(rows):
            for c in range(cols):
                if matrix[r][c] == 1:
                    islands += 1
                    dfs(r, c)

        return islands
```

## Solution 2: Iterative breadth-first search

At the constraint ceiling of 300 × 300, a grid of solid land is 90,000 connected cells, so the recursive depth-first search nests 90,000 frames deep — well past Python's default recursion limit of 1000. Replacing the call stack with an explicit queue removes that risk entirely and visits exactly the same cells.

The one detail that matters is marking cells when they are *pushed* rather than when they are popped. If you defer the mark until a cell comes off the queue, every neighbour that sees it pushes its own copy first, and the queue fills with duplicates.

### Complexity analysis

- Time complexity: $O(m \times n)$ — unchanged; each cell enters the queue at most once
- Space complexity: $O(\min(m, n))$ — the queue holds at most one frontier of the flood, which is bounded by the shorter side of the grid

```python
from collections import deque
from typing import List


class Solution:
    def num_islands(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])

        def bfs(start_r, start_c):
            queue = deque([(start_r, start_c)])
            matrix[start_r][start_c] = 0
            while queue:
                row, col = queue.popleft()
                for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                    nr, nc = row + dr, col + dc
                    if 0 <= nr < rows and 0 <= nc < cols and matrix[nr][nc] == 1:
                        matrix[nr][nc] = 0  # mark on push, not on pop
                        queue.append((nr, nc))

        islands = 0
        for r in range(rows):
            for c in range(cols):
                if matrix[r][c] == 0:
                    continue
                islands += 1
                bfs(r, c)
                       

        return islands
```
