---
title: Sum of Subarray Minimums
difficulty: Medium
leetcode: https://leetcode.com/problems/sum-of-subarray-minimums/
tags:
  - Array
  - Dynamic Programming
  - Stack
  - Monotonic Stack
---

# Sum of Subarray Minimums

## Problem description

Given an array of integers `arr`, return the sum of the minimum values of every contiguous subarray. Since the result can be very large, return it modulo $10^9 + 7$.

## Examples

**Example 1:**

```plaintext
Input:  arr = [3, 1, 2, 4, 5]
Output: 30
Explanation:
  Subarrays and their minimums:
  [3]=3  [1]=1  [2]=2  [4]=4  [5]=5
  [3,1]=1  [1,2]=1  [2,4]=2  [4,5]=4
  [3,1,2]=1  [1,2,4]=1  [2,4,5]=2
  [3,1,2,4]=1  [1,2,4,5]=1
  [3,1,2,4,5]=1
  Sum = 3+1+2+4+5+1+1+2+4+1+1+2+1+1+1 = 30
```

**Example 2:**

```plaintext
Input:  arr = [2, 6, 5, 4]
Output: 36
```

**Example 3:**

```plaintext
Input:  arr = [7, 3, 8]
Output: 27
```

## Constraints

- `1 <= arr.length <= 3 * 10^4`
- `1 <= arr[i] <= 3 * 10^4`

## Hints

<details>
<summary>Hint 1</summary>

Instead of iterating over all $O(n^2)$ subarrays, flip the question: for each element, how many subarrays have it as the minimum? Multiplying that count by the element's value gives its total contribution to the answer.

</details>

<details>
<summary>Hint 2</summary>

For `arr[i]` to be the minimum of a subarray, the subarray must not extend past the nearest smaller element on either side. How would you find those boundaries efficiently for every index?

</details>

## Solution

### Intuition

For each `arr[i]`, count the subarrays where it is the minimum. If the nearest strictly smaller element to the left is at index `left` and the nearest smaller-or-equal element to the right is at index `right`, then `arr[i]` is the minimum in every subarray that starts in `(left, i]` and ends in `[i, right)`. That is `(i - left) × (right - i)` subarrays, contributing `arr[i] × (i - left) × (right - i)` to the total.

The monotonic stack finds both boundaries in a single pass: when `arr[i]` causes a pop, the popped index `mid` now has its right boundary (`i`) and left boundary (the new stack top, or `-1`).

```plaintext
arr = [3, 1, 2, 4, 5]   (index 0..4)

arr = [3, 1, 2, 4, 5]

Phase 1 — scan:
  i=0 (3): push.               stack: [0]
  i=1 (1): 1 < 3 → pop j=0
    left=-1, right=1
    left_count=1, right_count=1  →  3 × 1 × 1 = 3
    push 1.                    stack: [1]
  i=2 (2): 2 > 1, push.        stack: [1, 2]
  i=3 (4): push.               stack: [1, 2, 3]
  i=4 (5): push.               stack: [1, 2, 3, 4]

Phase 2 — drain (no smaller element to the right, so right = len(arr) = 5):
  pop j=4: left=3, right=5  →  5 × 1 × 1 = 5
  pop j=3: left=2, right=5  →  4 × 1 × 2 = 8
  pop j=2: left=1, right=5  →  2 × 1 × 3 = 6
  pop j=1: left=-1, right=5 →  1 × 2 × 4 = 8

Total = 3 + 5 + 8 + 6 + 8 = 30 ✓
```

### Algorithm

**Phase 1 — scan:** for each element, pop everything it beats. The current index is the right boundary for each popped element; the new stack top is its left boundary.

**Phase 2 — drain:** elements still in the stack never found a smaller element to their right, so `right = len(arr)`.

### Complexity analysis

- Time complexity: $O(n)$ — each index is pushed and popped at most once
- Space complexity: $O(n)$ — the stack

```python
from typing import List


class Solution:
    def sumSubarrayMins(self, arr: List[int]) -> int:
        MOD = 10**9 + 7
        result = 0
        stack = []  # indices; unresolved elements waiting for their right boundary

        for i in range(len(arr)):
            while stack and arr[i] < arr[stack[-1]]:
                j = stack.pop()
                left = stack[-1] if stack else -1  # previous smaller element
                right = i                          # next smaller element (current)

                left_count = j - left   # subarrays can start anywhere in (left, j]
                right_count = right - j # subarrays can end anywhere in [j, right)

                result += arr[j] * left_count * right_count

            stack.append(i)

        # remaining elements have no smaller element to their right
        while stack:
            j = stack.pop()
            left = stack[-1] if stack else -1
            right = len(arr)

            left_count = j - left
            right_count = right - j

            result += arr[j] * left_count * right_count

        return result % MOD
```
