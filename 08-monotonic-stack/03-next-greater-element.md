---
title: Next Greater Element
difficulty: Easy
leetcode: https://leetcode.com/problems/next-greater-element-i/
tags:
  - Array
  - Hash Table
  - Stack
  - Monotonic Stack
---

# Next Greater Element

## Problem description

Given two integer arrays `nums1` and `nums2` where all integers in `nums1` appear in `nums2`, return an array `answer` where `answer[i]` is the next greater element for `nums1[i]` in `nums2`.

The next greater element of `x` in `nums2` is the first element to the right of `x` in `nums2` that is greater than `x`. If no such element exists, the answer for that position is `-1`.

## Examples

**Example 1:**

```plaintext
Input:  nums1 = [4, 2, 6], nums2 = [6, 2, 4, 5, 3, 7]
Output: [5, 4, 7]
Explanation: In nums2, 4's next greater is 5, 2's is 4, 6's is 7.
```

**Example 2:**

```plaintext
Input:  nums1 = [9, 7, 1], nums2 = [1, 7, 9, 5, 4, 3]
Output: [-1, 9, 7]
Explanation: 9 has no next greater. 7's next greater is 9. 1's next greater is 7.
```

**Example 3:**

```plaintext
Input:  nums1 = [5, 12, 3], nums2 = [12, 3, 5, 4, 10, 15]
Output: [10, 15, 5]
```

## Constraints

- `1 <= nums1.length <= nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 10^4`
- All integers in `nums1` and `nums2` are unique
- Every integer in `nums1` appears in `nums2`

## Hints

<details>
<summary>Hint 1</summary>

Process `nums2` once to build a map from every value to its next greater element. How do you find each element's next greater without scanning forward from every position?

</details>

<details>
<summary>Hint 2</summary>

Picture elements in `nums2` waiting for something larger to appear to their right. A stack can hold these "waiting" elements. When a new element arrives and is larger than the top, it is the answer for the top — pop and record. Keep going until the stack top is no longer smaller.

</details>

## Solution

### Intuition

Scan `nums2` left to right, maintaining a stack of elements that are still "waiting" for their next greater element. The stack stays in decreasing order — each new element, if larger than the top, resolves those waiting elements.

```plaintext
nums2 = [6, 2, 4, 5, 3, 7]

Visit 6: stack empty, push.            stack: [6]
Visit 2: 2 < 6, push.                  stack: [6, 2]
Visit 4: 4 > 2 → next_greater[2] = 4, pop.
         4 < 6, push.                  stack: [6, 4]
Visit 5: 5 > 4 → next_greater[4] = 5, pop.
         5 < 6, push.                  stack: [6, 5]
Visit 3: 3 < 5, push.                  stack: [6, 5, 3]
Visit 7: 7 > 3 → next_greater[3] = 7, pop.
         7 > 5 → next_greater[5] = 7, pop.
         7 > 6 → next_greater[6] = 7, pop.
         push.                         stack: [7]

Remaining in stack → no next greater (default -1).

next_greater = {2: 4, 4: 5, 3: 7, 5: 7, 6: 7}

nums1 = [4, 2, 6] → [5, 4, 7]
```

### Algorithm

1. Walk `nums2`; for each element pop all stack values smaller than it, mapping each to the current element
2. Elements left in the stack at the end have no next greater — they map to `-1`
3. For each element in `nums1`, look up its answer in the map

### Complexity analysis

- Time complexity: $O(m + n)$ — one pass over `nums2`, one lookup pass over `nums1`
- Space complexity: $O(n)$ — the stack and the map, both bounded by `len(nums2)`

```python
from typing import List


class Solution:
    def nextGreaterElement(self, nums1: List[int], nums2: List[int]) -> List[int]:
        next_greater = {}
        stack = []  # monotonic decreasing stack of unresolved values

        for num in nums2:
            while stack and stack[-1] < num:
                next_greater[stack.pop()] = num
            stack.append(num)

        return [next_greater.get(num, -1) for num in nums1]
```
