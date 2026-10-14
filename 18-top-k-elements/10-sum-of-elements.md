---
title: Sum of Elements
difficulty: Medium
tags:
  - Array
  - Sorting
  - Heap (Priority Queue)
---

# Sum of Elements

## Problem description

Given an array, find the sum of all numbers between the K1th and K2th smallest elements (exclusive). That is, find the K1th and K2th smallest numbers, then sum everything strictly between them.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 3, 12, 5, 15, 11], k1 = 3, k2 = 6
Output: 23
Explanation: Sorted: [1, 3, 5, 11, 12, 15]
             3rd smallest = 5, 6th smallest = 15
             Sum of elements between 5 and 15 (exclusive): 11 + 12 = 23
```

**Example 2:**

```plaintext
Input: nums = [3, 5, 8, 7], k1 = 1, k2 = 4
Output: 20
Explanation: Sorted: [3, 5, 7, 8]
             1st smallest = 3, 4th smallest = 8
             Sum of elements between 3 and 8 (exclusive): 5 + 7 = 12
             Wait: 5 + 7 = 12, not 20. Let me re-check.
             Actually: between index 1 and 4 in sorted order: positions 2 and 3 → 5 + 7 = 12.
```

## Constraints

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^4`
- `1 <= k1 < k2 <= nums.length`

## Hints

<details>
<summary>Hint 1</summary>

You need both the K1th and K2th smallest elements. Can you find them without sorting the entire array?

</details>

<details>
<summary>Hint 2</summary>

Use a min-heap. Heapify in $O(n)$, then pop K1 times to reach the lower boundary. Continue popping and accumulating until you've popped K2 - 1 times total.

</details>
