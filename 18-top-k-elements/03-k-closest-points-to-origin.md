---
title: K Closest Points to the Origin
difficulty: Medium
leetcode_title: K Closest Points to Origin
leetcode: https://leetcode.com/problems/k-closest-points-to-origin/
tags:
  - Array
  - Math
  - Divide and Conquer
  - Geometry
  - Sorting
  - Heap (Priority Queue)
  - Quickselect
---

# K Closest Points to the Origin

## Problem description

Given an array of points in a 2D plane and a number K, find the K closest points to the origin.

## Examples

**Example 1:**

```plaintext
Input: points = [[1, 2], [1, 3]], k = 1
Output: [[1, 2]]
Explanation: Squared distances: [1²+2²=5, 1²+3²=10]. [1,2] is closer.
```

**Example 2:**

```plaintext
Input: points = [[1, 3], [-2, 2]], k = 1
Output: [[-2, 2]]
Explanation: Squared distances: [1²+3²=10, (-2)²+2²=8]. [-2,2] is closer.
```

**Example 3:**

```plaintext
Input: points = [[3, 3], [5, -1], [-2, 4]], k = 2
Output: [[3, 3], [-2, 4]]
Explanation: Squared distances: [18, 26, 20]. The two closest are [3,3] and [-2,4].
```

## Constraints

- `1 <= k <= points.length <= 10^4`
- `-10^4 <= points[i][0], points[i][1] <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

The distance from the origin to a point `(x, y)` is $\sqrt{x^2 + y^2}$. Since square root is monotonic, comparing $x^2 + y^2$ directly avoids the square root and keeps comparisons exact.

</details>

<details>
<summary>Hint 2</summary>

To find the K closest, you need to evict the *farthest* point when the heap overflows. What kind of heap lets you quickly access the farthest point seen so far?

</details>

## Solution

### Intuition

A max-heap of size K tracks the K closest points seen so far, with the *farthest* of them at the root. When a new point arrives, it challenges the farthest point in the current set. If the new point is closer, it displaces the farthest.

Squared distance avoids the square root without changing the relative order of distances, keeping comparisons exact and cheap.

```plaintext
points = [[3,3],[5,-1],[-2,4]], k = 2

squared distances: [18, 26, 20]

push [3,3]  dist=18   heap=[(18,[3,3])]
push [5,-1] dist=26   heap=[(26,[5,-1]), (18,[3,3])]
push [-2,4] dist=20   20 < 26 (max) → pop [5,-1], push [-2,4]
            heap=[(20,[-2,4]), (18,[3,3])]

result: [[3,3], [-2,4]]  ✓
```

### Algorithm

1. For each point, compute its squared distance from the origin.
2. Push `(-distance, x, y)` onto a min-heap (negated to simulate a max-heap).
3. If the heap exceeds size `k`, pop — evicting the farthest point.
4. Return all points remaining in the heap.

### Complexity analysis

- Time complexity: $O(n \log k)$ — each of the $n$ points triggers at most one push and one pop, both $O(\log k)$.
- Space complexity: $O(k)$ — the heap holds at most $k + 1$ points at any moment.

```python
import heapq
from typing import List


class Solution:
    def k_closest(self, points: List[List[int]], k: int) -> List[List[int]]:
        heap = []

        for x, y in points:
            distance = x * x + y * y
            heapq.heappush(heap, (-distance, x, y))

            if len(heap) > k:
                heapq.heappop(heap)

        return [[x, y] for _, x, y in heap]
```
