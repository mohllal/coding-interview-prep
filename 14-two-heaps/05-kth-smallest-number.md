---
title: Kth Smallest Number
difficulty: Medium
leetcode_title: Kth Largest Element in an Array
leetcode: https://leetcode.com/problems/kth-largest-element-in-an-array/
tags:
  - Array
  - Divide and Conquer
  - Sorting
  - Heap (Priority Queue)
  - Quickselect
---

# Kth Smallest Number

## Problem description

Given an unsorted array of numbers, find the Kth smallest number in it.

Note that it is the Kth smallest in sorted order, not the Kth distinct element.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 5, 12, 2, 11, 5], k = 3
Output: 5
Explanation: Sorted: [1, 2, 5, 5, 11, 12]. The 3rd smallest is 5.
```

**Example 2:**

```plaintext
Input: nums = [1, 5, 12, 2, 11, 5], k = 4
Output: 5
Explanation: Sorted: [1, 2, 5, 5, 11, 12]. The 4th smallest is also 5.
```

**Example 3:**

```plaintext
Input: nums = [5, 12, 11, -1, 12], k = 3
Output: 11
Explanation: Sorted: [-1, 5, 11, 12, 12]. The 3rd smallest is 11.
```

## Hints

<details>
<summary>Hint 1</summary>

Sorting the array gives the answer in $O(n \log n)$. Can you avoid a full sort by only tracking the K smallest elements seen so far?

</details>

<details>
<summary>Hint 2</summary>

A max-heap of size K tracks the K smallest elements: the heap's maximum is the Kth smallest, because K-1 elements smaller than it are already in the heap. When a new element arrives, only swap it in if it's smaller than the current max.

</details>

## Solution 1: Max-heap of size K

### Intuition

We only care about the K smallest elements — not their relative order. A max-heap of size K tracks exactly this: the heap's maximum is the Kth smallest overall, because every other element in the heap is known to be smaller than it.

There are two phases:

1. **Fill:** push the first K elements unconditionally — no comparisons needed yet, any K elements are trivially the K smallest so far.
2. **Replace:** for each remaining element, only act if it's smaller than the heap's current max. If it is, pop the max (it's been displaced) and push the new element.

When done, the heap's max is the answer.

```plaintext
nums = [1, 5, 12, 2, 11, 5],  k = 3

--- fill phase (first k elements) ---
num=1   push          heap=[1]
num=5   push          heap=[5, 1]
num=12  push          heap=[12, 5, 1]

--- replace phase (remaining elements) ---
num=2   2 < 12 → pop 12, push 2    heap=[5, 2, 1]
num=11  11 > 5  → skip
num=5   5 = 5   → skip

top = 5  ✓
```

### Algorithm

1. Push the first `k` elements onto a max-heap.
2. For each remaining element `num`:
   - If `num < heap max`: pop the max, push `num`.
   - Otherwise skip — `num` is too large to be among the K smallest.
3. Return the heap's maximum — it is the Kth smallest.

### Complexity analysis

- Time complexity: $O(n \log k)$ — the fill phase does $k$ pushes at $O(\log k)$ each; the replace phase inspects the remaining $n - k$ elements and does at most one push/pop per element, also $O(\log k)$.
- Space complexity: $O(k)$ — the heap holds exactly $k$ elements throughout the replace phase.

```python
import heapq
from typing import List


class Solution:
    def kth_smallest(self, nums: List[int], k: int) -> int:
        heap = []

        # fill: push the first k elements unconditionally
        for i in range(k):
            heapq.heappush(heap, -nums[i])

        # replace: if a later element is smaller than the current max, swap it in
        for i in range(k, len(nums)):
            if -nums[i] > heap[0]:  # nums[i] < heap max
                heapq.heappop(heap)
                heapq.heappush(heap, -nums[i])

        return -heap[0]
```

## Solution 2: Heapify then pop K times

### Intuition

Convert the entire array into a min-heap in $O(n)$, then pop from it $k$ times. Each pop removes the current minimum, so after $k$ pops the last value removed is the Kth smallest.

### Algorithm

1. Heapify `nums` into a min-heap.
2. Pop $k - 1$ times to discard the first through $(k-1)$th smallest.
3. The next pop (or `heap[0]` after the loop) is the Kth smallest.

### Complexity analysis

- Time complexity: $O(n + k \log n)$ — $O(n)$ to heapify, then $k$ pops each costing $O(\log n)$.
- Space complexity: $O(n)$ — the heap is a copy of the full array.

```python
import heapq
from typing import List


class Solution:
    def kth_smallest(self, nums: List[int], k: int) -> int:
        heap = nums[:]
        heapq.heapify(heap)

        for _ in range(k - 1):
            heapq.heappop(heap)

        return heap[0]
```

## Solution 3: QuickSelect

### Intuition

QuickSelect adapts QuickSort's partition step but makes one additional observation: we don't need to sort everything, we just need the Kth smallest.

It works by partitioning the array around a pivot — rearranging so every element smaller than the pivot ends up to its left and every element larger ends up to its right. After that, the pivot is in its **final sorted position** and we know exactly how many elements sit to its left. Call that count `p`:

- If `p + 1 == k`: the pivot **is** the Kth smallest. Done.
- If `p + 1 > k`: the Kth smallest is somewhere in the left half — ignore the right half entirely.
- If `p + 1 < k`: the Kth smallest is in the right half — ignore the left half entirely.

Each partition throws away one half, so the total work is roughly $n + n/2 + n/4 + \ldots = 2n$, which is $O(n)$ on average.

```plaintext
nums = [1, 5, 12, 2, 11, 5],  k = 3  (target index = 2)

Step 1: pivot = 5 (last element)
  before:  [1,  5, 12,  2, 11,  5]
  after:   [1,  5,  2 | 5 | 11, 12]
                          ↑ pivot at index 3

  3 elements to its left  →  pivot is the 4th smallest
  target index 2 < 3  →  search left half only

Step 2: pivot = 2 within [1, 5, 2]
  before:  [1,  5,  2]
  after:   [1 | 2 |  5]
                ↑ pivot at index 1

  1 element to its left  →  pivot is the 2nd smallest
  target index 2 > 1  →  search right half only

Step 3: left = right = index 2  →  nums[2] = 5  ✓
```

A random pivot avoids the $O(n^2)$ worst case that occurs when the pivot always lands at an extreme (e.g., a sorted array with a fixed last-element pivot).

### Algorithm

1. Copy the array so the original is not modified.
2. Set `left = 0`, `right = n - 1`, `target = k - 1`.
3. While `left < right`:
   - Choose a random pivot, swap it to `right`.
   - Partition: move all elements ≤ pivot before `store`, then place the pivot at `store`.
   - If `store == target`: stop.
   - If `target < store`: set `right = store - 1`.
   - If `target > store`: set `left = store + 1`.
4. Return `nums[target]`.

### Complexity analysis

- Time complexity: $O(n)$ average — each partition cuts the search space roughly in half; $O(n^2)$ worst case, made extremely unlikely by random pivot selection.
- Space complexity: $O(1)$ — partitioning is done in place; no recursion stack.

```python
import random
from typing import List


class Solution:
    def kth_smallest(self, nums: List[int], k: int) -> int:
        nums = nums[:]
        left, right, target = 0, len(nums) - 1, k - 1

        while left < right:
            pivot_idx = self._partition(nums, left, right)
            if pivot_idx == target:
                break
            elif target < pivot_idx:
                right = pivot_idx - 1
            else:
                left = pivot_idx + 1

        return nums[target]

    def _partition(self, nums: List[int], left: int, right: int) -> int:
        pivot_pos = random.randint(left, right)
        nums[pivot_pos], nums[right] = nums[right], nums[pivot_pos]
        pivot = nums[right]

        store = left
        for i in range(left, right):
            if nums[i] <= pivot:
                nums[store], nums[i] = nums[i], nums[store]
                store += 1

        nums[store], nums[right] = nums[right], nums[store]
        return store
```
