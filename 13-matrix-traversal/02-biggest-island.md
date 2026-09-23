---
title: Biggest Island
difficulty: Medium
leetcode_title: Max Area of Island
leetcode: https://leetcode.com/problems/max-area-of-island/
tags:
  - Array
  - Depth-First Search
  - Breadth-First Search
  - Union-Find
  - Matrix
---

# Biggest Island

## Problem description

Given a 2D matrix containing only `1`s (land) and `0`s (water), find the biggest island in it and return its area — the number of land cells it contains.

An island is a connected set of `1`s surrounded by either an edge of the matrix or `0`s. Cells are connected horizontally and vertically, not diagonally.

## Examples

**Example 1:**

```plaintext
Input: matrix = [[0, 1, 1, 0, 0],
                 [1, 1, 1, 0, 0],
                 [0, 0, 0, 1, 0],
                 [0, 0, 0, 0, 1]]
Output: 5
Explanation: Three islands, of areas 5, 1 and 1. The biggest is the
             five-cell block in the top-left.

0 1 1 0 0
1 1 1 0 0
0 0 0 1 0
0 0 0 0 1
```

**Example 2:**

```plaintext
Input: matrix = [[0, 0, 0],
                 [0, 0, 0]]
Output: 0
Explanation: No land at all, so the biggest island has area 0.
```

## Constraints

- `m == len(matrix)`
- `n == len(matrix[i])`
- `1 <= m, n <= 50`
- `matrix[i][j]` is `0` or `1`

## Hints

<details>
<summary>Hint 1</summary>

This is [Number of Islands](./01-number-of-islands.md) with one change. There, the flood existed only to stop the outer scan double-counting, and threw away everything it learned. What would you have to make it hand back instead?

</details>

<details>
<summary>Hint 2</summary>

Let the flood return the number of cells it sank: `1` for the current cell, plus whatever the four recursive calls report. The outer scan then keeps a running maximum over the values it gets back.

</details>

## Solution

### Intuition

This is [Number of Islands](./01-number-of-islands.md) with the depth-first search upgraded from a procedure to a function.

There, the depth-first search sank an island purely so the outer scan would not count it twice, and returned nothing. Here the outer scan still needs that, but it also wants to know how large each island was — and the search already visits exactly those cells, so it can count them on the way.

Each call contributes `1` for the cell it just sank, then adds whatever its four neighbours report. Out-of-bounds and water contribute `0`:

```plaintext
0 1 1 0 0
1 1 1 0 0
0 0 0 1 0
0 0 0 0 1

DFS from (0,1)  ->  sinks (0,1) (0,2) (1,1) (1,0) (1,2)   area 5   max = 5
DFS from (2,3)  ->  sinks (2,3)                            area 1   max = 5
DFS from (3,4)  ->  sinks (3,4)                            area 1   max = 5

answer = 5
```

Seeding the maximum at `0` rather than `-inf` is what makes an all-water grid return `0` without a special case.

### Algorithm

1. Walk every cell of the grid in row-major order
2. Skip any cell that is water
3. On land, run a depth-first search from that cell and keep the returned area if it beats the best so far
4. The depth-first search returns `0` if the cell is out of bounds or is water
5. Otherwise it sets the cell to `0` and returns `1` plus the sum of the four recursive calls
6. Return the best area seen

### Complexity analysis

- Time complexity: $O(m \times n)$ — the outer scan inspects each cell once, and all the searches combined enter each cell at most once
- Space complexity: $O(m \times n)$ — the recursion stack in the worst case of a grid that is entirely land

```python
from typing import List


class Solution:
    def max_area_of_island(self, matrix: List[List[int]]) -> int:
        if not matrix or not matrix[0]:
            return 0

        rows, cols = len(matrix), len(matrix[0])

        def dfs(r: int, c: int) -> int:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return 0

            if matrix[r][c] == 0:
                return 0

            matrix[r][c] = 0
            return 1 + sum(
                dfs(r + dr, c + dc)
                for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1))
            )

        biggest = 0
        for r in range(rows):
            for c in range(cols):
                if matrix[r][c] == 1:
                    biggest = max(biggest, dfs(r, c))

        return biggest
```
