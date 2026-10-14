---
title: Top K Frequent Numbers
difficulty: Medium
leetcode_title: Top K Frequent Elements
leetcode: https://leetcode.com/problems/top-k-frequent-elements/
tags:
  - Array
  - Hash Table
  - Divide and Conquer
  - Sorting
  - Heap (Priority Queue)
  - Bucket Sort
  - Counting
  - Quickselect
---

# Top K Frequent Numbers

## Problem description

Given an unsorted array of numbers, find the K most frequently appearing numbers.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 3, 5, 12, 11, 12, 11], k = 2
Output: [12, 11]
Explanation: 12 appears twice, 11 appears twice, all others once.
             Both 12 and 11 are the top 2 most frequent.
```

**Example 2:**

```plaintext
Input: nums = [5, 12, 11, 3, 11], k = 2
Output: [11, 5] or [11, 12] or [11, 3]
Explanation: 11 appears twice (most frequent). The remaining elements
             each appear once — any two of them complete the answer.
```

## Constraints

- `1 <= nums.length <= 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `k` is in the range `[1, the number of unique elements in nums]`
- The answer is unique (guaranteed by the problem).

## Hints

<details>
<summary>Hint 1</summary>

You need the elements with the highest frequency. How do you efficiently count each element's frequency? Once you have counts, how does this reduce to a "K largest" problem?

</details>

<details>
<summary>Hint 2</summary>

After counting frequencies, each `(frequency, number)` pair becomes a candidate. You need the K pairs with the largest frequency — which is exactly the Top K pattern applied to frequencies instead of values.

</details>

## Solution

### Intuition

Two steps: count, then select. First use a hash map to count the frequency of every number in $O(n)$. Then apply the Top K pattern on the `(frequency, number)` pairs to find the K most frequent.

For the selection step, a min-heap of size K — keyed by frequency — evicts the least frequent element whenever the heap overflows. At the end, the heap holds the K most frequent numbers.

```plaintext
nums = [1, 3, 5, 12, 11, 12, 11], k = 2

frequencies: {1:1, 3:1, 5:1, 12:2, 11:2}

push (1,1)   heap=[(1,1)]
push (1,3)   heap=[(1,1),(1,3)]   → overflow → pop (1,1)   heap=[(1,3)]

  wait, k=2, no overflow yet with 2 elements...

push (1,1)   heap=[(1,1)]
push (1,3)   heap=[(1,1),(1,3)]
push (1,5)   overflow → pop (1,1)   heap=[(1,3),(1,5)]
push (2,12)  2 > 1 (min) → pop (1,3), push (2,12)   heap=[(1,5),(2,12)]
push (2,11)  2 > 1 (min) → pop (1,5), push (2,11)   heap=[(2,11),(2,12)]

result: [11, 12]  ✓
```

### Algorithm

1. Count each number's frequency using a hash map.
2. For each `(number, frequency)` pair:
   - Push `(frequency, number)` onto a min-heap.
   - If the heap exceeds size `k`, pop the minimum — evicting the least frequent element.
3. Return the numbers remaining in the heap.

### Complexity analysis

- Time complexity: $O(n \log k)$ — $O(n)$ to count, then $O(n \log k)$ to process all unique numbers with the heap.
- Space complexity: $O(n)$ — the frequency map holds up to $n$ entries; the heap holds $k$.

```python
import heapq
from collections import Counter
from typing import List


class Solution:
    def top_k_frequent(self, nums: List[int], k: int) -> List[int]:
        freq = Counter(nums)
        heap = []

        for num, count in freq.items():
            heapq.heappush(heap, (count, num))

            if len(heap) > k:
                heapq.heappop(heap)

        return [num for _, num in heap]
```
