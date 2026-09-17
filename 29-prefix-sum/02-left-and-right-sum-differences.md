---
title: Left and Right Sum Differences
difficulty: Easy
leetcode: https://leetcode.com/problems/left-and-right-sum-differences/
tags:
  - Array
  - Prefix Sum
---

# Left and Right Sum Differences

## Problem description

Given an integer array `nums`, return an array `answer` of the same length where `answer[i]` is the absolute difference between the sum of all elements to the left of index `i` and the sum of all elements to the right of index `i`.

If there are no elements to the left or right, the corresponding sum is `0`.

## Examples

**Example 1:**

```plaintext
Input: nums = [2, 5, 1, 6, 1]
Output: [13, 6, 0, 7, 14]
Explanation:
  i=0: |0 - (5+1+6+1)| = 13
  i=1: |2 - (1+6+1)|   = 6
  i=2: |(2+5) - (6+1)| = 0
  i=3: |(2+5+1) - 1|   = 7
  i=4: |(2+5+1+6) - 0| = 14
```

**Example 2:**

```plaintext
Input: nums = [3, 3, 3]
Output: [6, 0, 6]
```

**Example 3:**

```plaintext
Input: nums = [1, 2, 3, 4, 5]
Output: [14, 11, 6, 1, 10]
```

## Constraints

- `1 <= nums.length <= 1000`
- `0 <= nums[i] <= 10^5`

## Hints

<details>
<summary>Hint 1</summary>

Computing both the left sum and the right sum fresh at every index is O(n) per index. Can you precompute something so each index only needs O(1) work?

</details>

<details>
<summary>Hint 2</summary>

Keep a running `left_sum` as you scan left to right. The right sum is `total - left_sum - nums[i]`. Both values are available in O(1) at every step.

</details>

## Solution

### Intuition

This is a direct application of the running-sum technique from [Find the Middle Index in Array](./01-find-the-middle-index-in-array.md): maintain a running left sum and derive the right sum as `total - left_sum - nums[i]`.

### Algorithm

1. Compute the total sum of the array
2. Walk left to right, maintaining `left_sum = 0`
3. At each index, compute `right_sum = total - left_sum - nums[i]`
4. Append `abs(left_sum - right_sum)` to the result
5. Add `nums[i]` to `left_sum` before moving on

### Complexity analysis

- Time complexity: $O(n)$ — one pass for the total, one pass to build the result
- Space complexity: $O(n)$ — the output array; O(1) auxiliary

```python
from typing import List


class Solution:
    def left_right_difference(self, nums: List[int]) -> List[int]:
        total = sum(nums)
        left_sum = 0
        result = []
        for num in nums:
            right_sum = total - left_sum - num
            result.append(abs(left_sum - right_sum))
            left_sum += num
        return result
```
