---
title: Flood Fill
difficulty: Easy
leetcode: https://leetcode.com/problems/flood-fill/
tags:
  - Array
  - Depth-First Search
  - Breadth-First Search
  - Matrix
---

# Flood Fill

## Problem description

An image is represented by a 2D matrix where each cell holds a pixel value.

The flood fill algorithm takes a starting cell and a color, and applies that color to every cell reachable from the start through horizontally and vertically connected cells that share the starting cell's original color. It spreads until it meets a cell of a different color, or the edge of the image.

Given a matrix, a starting cell and a color, flood fill the matrix and return it.

## Examples

**Example 1:**

```plaintext
Input: matrix = [[1, 1, 1, 1, 0],
                 [1, 1, 0, 1, 1],
                 [1, 0, 1, 1, 0],
                 [1, 1, 1, 0, 1]]
       start = (1, 3), color = 2
Output: [[2, 2, 2, 2, 0],
         [2, 2, 0, 2, 2],
         [2, 0, 2, 2, 0],
         [2, 2, 2, 0, 1]]

before              after
1 1 1 1 0           2 2 2 2 0
1 1 0 1 1    -->    2 2 0 2 2
1 0 1 1 0           2 0 2 2 0
1 1 1 0 1           2 2 2 0 1
                            ^
Explanation: every 1 reachable from (1, 3) becomes 2. The 1 at (3, 4) is
             walled off by 0s at (2, 4) and (3, 3), so it is left alone.
```

**Example 2:**

```plaintext
Input: matrix = [[0, 0, 0],
                 [0, 1, 1]]
       start = (1, 1), color = 1
Output: [[0, 0, 0],
         [0, 1, 1]]
Explanation: the starting cell is already color 1, so there is nothing to do.
```

## Constraints

- `m == len(matrix)`
- `n == len(matrix[i])`
- `1 <= m, n <= 50`
- `0 <= matrix[i][j], color < 2^16`
- The starting cell is inside the matrix

## Hints

<details>
<summary>Hint 1</summary>

There is no outer scan here — the problem hands you the seed. You only need the flood itself, spreading while cells match the starting cell's *original* color.

</details>

<details>
<summary>Hint 2</summary>

Read the original color before you overwrite anything, otherwise the first write changes the very value you are comparing against. Then consider what happens when the new color already equals the original one: what stops the traversal?

</details>

## Solution

### Intuition

This is the flood on its own, with no driver loop. The problem gives you the starting cell, so there is exactly one region to visit and no risk of counting anything twice.

The traversal spreads while cells match the starting cell's original color, and recoloring a cell serves as the visited marker — once it holds the new color it no longer matches, so the recursion will not step back into it.

That doubling-up is also where the one trap lives. If the new color already equals the original, then recoloring changes nothing observable, no cell ever stops matching, and two adjacent cells send the traversal back and forth forever. An early return when `original == color` is what prevents it:

```plaintext
1 1 1 1 0                 start = (1,3), original = 1, color = 2
1 1 0 1 1
1 0 1 1 0
1 1 1 0 1

(1,3) matches 1  ->  set to 2, spread to (0,3) (2,3) (1,2) (1,4)
(1,2) holds 0    ->  stop, different color
(1,4) matches 1  ->  set to 2, spread on
...
(3,4) never reached: (2,4) is 0 and (3,3) is 0, so nothing connects to it

2 2 2 2 0
2 2 0 2 2
2 0 2 2 0
2 2 2 0 1
```

### Algorithm

1. Read the starting cell's color into `original`
2. If `original` already equals the requested color, return the matrix unchanged
3. Flood from the starting cell
4. The flood returns immediately if the cell is out of bounds or does not hold `original`
5. Otherwise it writes the new color into the cell and recurses into all four neighbours
6. Return the matrix

### Complexity analysis

- Time complexity: $O(m \times n)$ — each cell is recolored at most once, and a recolored cell no longer matches `original`, so it is never re-entered
- Space complexity: $O(m \times n)$ — the recursion stack when the entire image is one color

```python
from typing import List


class Solution:
    def flood_fill(
        self, matrix: List[List[int]], row: int, col: int, color: int
    ) -> List[List[int]]:
        original = matrix[row][col]
        if original == color:
            # recoloring would be invisible, so nothing would ever look visited
            return matrix

        rows, cols = len(matrix), len(matrix[0])

        def dfs(r: int, c: int) -> None:
            if r < 0 or r >= rows or c < 0 or c >= cols:
                return

            if matrix[r][c] != original:
                return

            matrix[r][c] = color
            for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
                dfs(r + dr, c + dc)

        dfs(row, col)
        return matrix
```
