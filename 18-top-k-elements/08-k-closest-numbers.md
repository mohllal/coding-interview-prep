---
title: K Closest Numbers
difficulty: Medium
leetcode_title: Find K Closest Elements
leetcode: https://leetcode.com/problems/find-k-closest-elements/
tags:
  - Array
  - Two Pointers
  - Binary Search
  - Sorting
  - Heap (Priority Queue)
---

# K Closest Numbers

## Problem description

Given a sorted number array and two integers K and X, find K closest numbers to X in the array. Return the numbers in sorted order. If two numbers have the same distance to X, prefer the smaller number.

## Examples

**Example 1:**

```plaintext
Input: arr = [5, 6, 7, 8, 9], k = 3, x = 7
Output: [6, 7, 8]
Explanation: Distances from 7: [2, 1, 0, 1, 2]. The 3 closest are 6, 7, 8.
```

**Example 2:**

```plaintext
Input: arr = [2, 4, 5, 6, 9], k = 3, x = 6
Output: [4, 5, 6]
Explanation: Distances from 6: [4, 2, 1, 0, 3]. The 3 closest are 4, 5, 6.
```

**Example 3:**

```plaintext
Input: arr = [2, 4, 5, 6, 9], k = 3, x = 10
Output: [5, 6, 9]
Explanation: Distances from 10: [8, 6, 5, 4, 1]. The 3 closest are 5, 6, 9.
```

## Constraints

- `1 <= k <= arr.length`
- `1 <= arr.length <= 10^4`
- `arr` is sorted in ascending order.
- `-10^4 <= arr[i], x <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

The array is already sorted. The K closest elements always form a contiguous window of size K. How do you find where that window starts?

</details>

<details>
<summary>Hint 2</summary>

Alternatively, use a max-heap of size K. The heap key must encode both distance and value so that, among ties, the larger (worse) number is evicted first.

</details>

## Solution 1: Max-heap

### Intuition

A max-heap of size K keeps the K closest numbers seen so far, with the farthest (worst) at the root. When the heap overflows, the root — farthest from X and, on a tie, the larger number — is evicted.

The heap key encodes both eviction priorities: `(-distance, -value)`. Python's min-heap pops the minimum tuple, so:
- Largest distance → most negative `-distance` → popped first. ✓
- Equal distance, larger value → more negative `-value` → popped first. ✓

```plaintext
arr = [2, 4, 5, 6, 9], k = 3, x = 6

keys: (dist, -dist, -num) for tracing

num=2  dist=4  push (-4, -2)   heap=[(-4,-2)]
num=4  dist=2  push (-2, -4)   heap=[(-4,-2),(-2,-4)]
num=5  dist=1  push (-1, -5)   heap=[(-4,-2),(-2,-4),(-1,-5)]
num=6  dist=0  push (-0, -6)   → heap size=4 > k=3
                pop min=(-4,-2) → evict num=2
                heap=[(-2,-4),(-1,-5),(0,-6)]
num=9  dist=3  push (-3, -9)   → pop min=(-3,-9) → evict num=9
                heap=[(-2,-4),(-1,-5),(0,-6)]

result: sorted(4, 5, 6) = [4, 5, 6]  ✓
```

### Algorithm

1. For each number in the array, compute its distance from X.
2. Push `(-distance, -number)` onto a min-heap.
3. If the heap exceeds size `k`, pop — evicting the farthest (or, on a tie, the largest) number.
4. Return the remaining numbers sorted.

### Complexity analysis

- Time complexity: $O(n \log k)$ — each of the $n$ elements triggers at most one push and one pop, both $O(\log k)$. Sorting the result is $O(k \log k)$.
- Space complexity: $O(k)$ — the heap holds at most $k + 1$ elements at any moment.

```python
import heapq
from typing import List


class Solution:
    def find_closest_elements(self, arr: List[int], k: int, x: int) -> List[int]:
        heap: List[tuple] = []

        for num in arr:
            dist = abs(num - x)
            heapq.heappush(heap, (-dist, -num))
            if len(heap) > k:
                heapq.heappop(heap)

        return sorted(-num for _, num in heap)
```

## Solution 2: Binary search

### Intuition

Since the array is sorted, the K closest elements always form a contiguous window. Binary search finds the optimal window start directly.

At each midpoint `mid`, we compare the distance from X to the leftmost candidate `arr[mid]` versus the rightmost candidate `arr[mid + k]`:
- If `x - arr[mid] > arr[mid + k] - x`: the right boundary is closer, so the window should shift right → `left = mid + 1`.
- Otherwise: the left boundary is closer or equidistant. Equal distances prefer the smaller number, so keep the left boundary → `right = mid`.

```plaintext
arr = [2, 4, 5, 6, 9], k = 3, x = 6
search range: left=0, right=5-3=2  (window starts between indices 0 and 2)

mid=1: arr[1]=4, arr[1+3]=arr[4]=9
  x - arr[mid] = 6 - 4 = 2
  arr[mid+k] - x = 9 - 6 = 3
  2 < 3  →  right = 1

mid=0: arr[0]=2, arr[0+3]=arr[3]=6
  x - arr[mid] = 6 - 2 = 4
  arr[mid+k] - x = 6 - 6 = 0
  4 > 0  →  left = 1

left == right == 1  →  window: arr[1:4] = [4, 5, 6]  ✓
```

### Complexity analysis

- Time complexity: $O(\log(n - k) + k)$ — binary search over $n - k$ positions, then $O(k)$ to return the window.
- Space complexity: $O(1)$ — no extra space beyond the output slice.

```python
from typing import List


class Solution:
    def find_closest_elements(self, arr: List[int], k: int, x: int) -> List[int]:
        left, right = 0, len(arr) - k

        while left < right:
            mid = (left + right) // 2
            if x - arr[mid] > arr[mid + k] - x:
                left = mid + 1
            else:
                right = mid

        return arr[left : left + k]
```
