---
title: Top K Numbers
difficulty: Easy
tags:
  - Array
  - Heap (Priority Queue)
---

# Top K Numbers

## Problem description

Given an unsorted array of numbers, find the K largest numbers in it.

## Examples

**Example 1:**

```plaintext
Input: nums = [3, 1, 5, 12, 2, 11], k = 3
Output: [5, 12, 11]
Explanation: The three largest numbers are 5, 11, and 12.
```

**Example 2:**

```plaintext
Input: nums = [5, 12, 11, -1, 12], k = 3
Output: [11, 12, 12]
Explanation: The three largest numbers are 11, 12, and 12 (duplicate).
```

## Constraints

- `1 <= k <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

Sorting the entire array gives the answer in $O(n \log n)$. Can you avoid a full sort by only keeping track of the K largest elements seen so far?

</details>

<details>
<summary>Hint 2</summary>

A min-heap of size K tracks the K largest elements. Its root is the smallest of the K largest — the first to evict when a larger element arrives.

</details>

## Solution

### Intuition

A min-heap of size K is the right choice here: the heap holds the K largest elements seen so far, with the *smallest* of them at the root. Each new element only needs to beat the root to earn a spot.

When a new element arrives:

- If it's larger than the heap's minimum, it belongs in the top K. Pop the current minimum (displaced) and push the new element.
- Otherwise skip — it's not among the K largest.

After processing all elements, the heap holds exactly the K largest.

```plaintext
nums = [3, 1, 5, 12, 2, 11], k = 3

--- fill (first k elements) ---
push 3     heap=[3]
push 1     heap=[1, 3]
push 5     heap=[1, 3, 5]

--- replace (remaining elements) ---
num=12  12 > 1  → pop 1, push 12   heap=[3, 12, 5]
num=2    2 < 3  → skip
num=11  11 > 3  → pop 3, push 11   heap=[5, 12, 11]

result: [5, 12, 11]  ✓
```

### Algorithm

1. Push the first `k` elements onto a min-heap.
2. For each remaining element `num`:
   - If `num > heap minimum`: pop the minimum, push `num`.
   - Otherwise skip.
3. Return the heap contents — they are the K largest.

### Complexity analysis

- Time complexity: $O(n \log k)$ — fill does $k$ pushes at $O(\log k)$ each; replace inspects $n - k$ elements with at most one push/pop per element, also $O(\log k)$.
- Space complexity: $O(k)$ — the heap holds exactly $k$ elements throughout.

```python
import heapq
from typing import List


class Solution:
    def top_k_numbers(self, nums: List[int], k: int) -> List[int]:
        heap = []

        for i in range(k):
            heapq.heappush(heap, nums[i])

        for i in range(k, len(nums)):
            if nums[i] > heap[0]:
                heapq.heappop(heap)
                heapq.heappush(heap, nums[i])

        return list(heap)
```
