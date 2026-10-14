---
title: Kth Largest Number in a Stream
difficulty: Easy
leetcode_title: Kth Largest Element in a Stream
leetcode: https://leetcode.com/problems/kth-largest-element-in-a-stream/
tags:
  - Tree
  - Design
  - Binary Search Tree
  - Heap (Priority Queue)
  - Binary Tree
  - Data Stream
---

# Kth Largest Number in a Stream

## Problem description

Design a class to find the Kth largest number in a stream of numbers. The class should support two operations:

- `__init__(k, nums)`: initialize the class with integer `k` and a list of initial numbers.
- `add(val)`: append `val` to the stream and return the Kth largest number.

## Examples

**Example 1:**

```plaintext
Input:
  k = 3, nums = [4, 5, 8, 2]
  add(3) → 4
  add(5) → 5
  add(10) → 5
  add(9) → 8
  add(4) → 8

Explanation:
  After nums:    sorted = [2, 4, 5, 8]   → 3rd largest = 4
  add(3)  = [2, 3, 4, 5, 8]             → 3rd largest = 4
  add(5)  = [2, 3, 4, 5, 5, 8]          → 3rd largest = 5
  add(10) = [2, 3, 4, 5, 5, 8, 10]      → 3rd largest = 5
  add(9)  = [2, 3, 4, 5, 5, 8, 9, 10]   → 3rd largest = 8
  add(4)  = [2, 3, 4, 4, 5, 5, 8, 9, 10]→ 3rd largest = 8
```

## Constraints

- `1 <= k <= 10^4`
- `0 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `-10^4 <= val <= 10^4`
- At most `10^4` calls will be made to `add`.
- It is guaranteed that there will be at least `k` elements in the array when you search for the Kth element.

## Hints

<details>
<summary>Hint 1</summary>

After each `add`, you need the Kth largest element in $O(\log k)$ time. What data structure holds exactly the K largest elements seen so far and gives you the Kth (the smallest of those K) in $O(1)$?

</details>

<details>
<summary>Hint 2</summary>

A min-heap of size K always contains the K largest elements. Its root is the smallest among them — which is the Kth largest overall. Adding a new element takes $O(\log k)$ and the answer is always `heap[0]`.

</details>

## Solution

### Intuition

A min-heap of exactly size K is the right structure: it holds the K largest numbers seen so far, with the Kth largest at the root (the heap's minimum). When a new number arrives:

- If it's larger than the root, it belongs in the top K. Pop the root (displaced) and push the new number.
- If the heap hasn't reached size K yet, push unconditionally.

After each `add`, `heap[0]` is the Kth largest — no extra work needed.

```plaintext
k = 3, nums = [4, 5, 8, 2]

Initial heap building:
push 4   heap=[4]
push 5   heap=[4,5]
push 8   heap=[4,5,8]
push 2   2 < 4 (root) → skip   heap=[4,5,8]

add(3):  3 < 4 → skip   heap=[4,5,8]   return 4  ✓
add(5):  5 > 4 → pop 4, push 5   heap=[5,5,8]   return 5  ✓
```

### Algorithm

1. Initialize a min-heap and call `add` on each element in `nums` to build the initial state.
2. For each `add(val)`:
   - Push `val` onto the heap.
   - If the heap grows beyond size `k`, pop the minimum.
   - Return `heap[0]` — the Kth largest.

### Complexity analysis

- Time complexity: $O(n \log k)$ for initialization ($n$ calls to `add`), $O(\log k)$ per subsequent `add`.
- Space complexity: $O(k)$ — the heap holds exactly $k$ elements after initialization.

```python
import heapq
from typing import List


class KthLargest:
    def __init__(self, k: int, nums: List[int]) -> None:
        self.k = k
        self.heap = []
        for num in nums:
            self.add(num)

    def add(self, val: int) -> int:
        heapq.heappush(self.heap, val)
        if len(self.heap) > self.k:
            heapq.heappop(self.heap)

        return self.heap[0]
```
