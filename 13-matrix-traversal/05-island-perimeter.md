---
title: Island Perimeter
difficulty: Easy
leetcode: https://leetcode.com/problems/island-perimeter/
tags:
  - Array
  - Depth-First Search
  - Breadth-First Search
  - Matrix
---

# Island Perimeter

## Problem description

You are given a 2D matrix containing only `1`s (land) and `0`s (water). The matrix has exactly one island — a connected set of `1`s surrounded by water or the edge of the matrix. Cells are connected horizontally and vertically, not diagonally.

The island may contain lakes: water cells fully enclosed by land. Those inner walls count toward the perimeter just as the outer edge does.

Find the perimeter of the island. The perimeter is the total number of cell sides that border water or the edge of the matrix.

## Examples

**Example 1:**

```plaintext
Input: matrix = [[0, 0, 0, 0, 0],
                 [0, 1, 0, 0, 0],
                 [0, 1, 1, 1, 0],
                 [0, 1, 0, 0, 0],
                 [0, 1, 0, 0, 0],
                 [0, 0, 0, 0, 0]]
Output: 14

0 0 0 0 0
0 1 0 0 0
0 1 1 1 0
0 1 0 0 0
0 1 0 0 0
0 0 0 0 0
```

**Example 2:**

```plaintext
Input: matrix = [[0, 0, 0, 0, 0, 0],
                 [0, 1, 0, 0, 0, 0],
                 [0, 1, 1, 1, 1, 0],
                 [0, 0, 0, 0, 0, 0]]
Output: 12

0 0 0 0 0 0
0 1 0 0 0 0
0 1 1 1 1 0
0 0 0 0 0 0
```

**Example 3:**

```plaintext
Input: matrix = [[1, 1, 1],
                 [1, 0, 1],
                 [1, 1, 1]]
Output: 16

1 1 1
1 0 1    <- lake: the inner wall adds 4 to the perimeter
1 1 1

Outer wall: 12   Inner wall (lake): 4   Total: 16
```

## Constraints

- `1 <= len(matrix), len(matrix[0]) <= 100`
- `matrix[i][j]` is `0` or `1`
- The grid contains exactly one island

## Hints

<details>
<summary>Hint 1</summary>

Every land cell starts with four sides. Each side it shares with another land cell is hidden from the perimeter. How many sides does each shared edge remove?

</details>

<details>
<summary>Hint 2</summary>

You do not need a flood fill here. A single scan works: for each land cell, count how many of its four neighbors are water or out of bounds. That count is the cell's contribution to the perimeter.

</details>

## Solution

### Intuition

Each land cell contributes one unit of perimeter for every side that faces water or the edge of the matrix. Counting those exposed sides directly — no DFS needed — gives the answer in one pass.

```plaintext
matrix = [[0, 1, 0],
          [1, 1, 1],
          [0, 1, 0]]

Cell (0,1): neighbors → up=edge, right=0, down=1, left=0 → exposed: 3
Cell (1,0): neighbors → up=0, right=1, down=0, left=edge → exposed: 3
Cell (1,1): neighbors → up=1, right=1, down=1, left=1    → exposed: 0
Cell (1,2): neighbors → up=0, right=edge, down=0, left=1 → exposed: 3
Cell (2,1): neighbors → up=1, right=0, down=edge, left=0 → exposed: 3

Perimeter = 3 + 3 + 0 + 3 + 3 = 12
```

### Algorithm

1. Scan every cell in the matrix
2. For each land cell, check all four neighbors
3. Add 1 to the perimeter for each neighbor that is out of bounds or water
4. Return the perimeter

### Complexity analysis

- Time complexity: $O(m \times n)$ — every cell is visited once
- Space complexity: $O(1)$ — no auxiliary data structures

```python
from typing import List


class Solution:
    def island_perimeter(self, matrix: List[List[int]]) -> int:
        rows, cols = len(matrix), len(matrix[0])
        perimeter = 0

        for r in range(rows):
            for c in range(cols):
                if matrix[r][c] == 0:
                    continue

                # up
                if r - 1 < 0 or matrix[r - 1][c] == 0:
                    perimeter += 1

                # down
                if r + 1 >= rows or matrix[r + 1][c] == 0:
                    perimeter += 1

                # left
                if c - 1 < 0 or matrix[r][c - 1] == 0:
                    perimeter += 1

                # right
                if c + 1 >= cols or matrix[r][c + 1] == 0:
                    perimeter += 1

        return perimeter
```
