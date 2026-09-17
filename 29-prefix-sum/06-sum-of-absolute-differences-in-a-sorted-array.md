---
title: Sum of Absolute Differences in a Sorted Array
difficulty: Medium
leetcode: https://leetcode.com/problems/sum-of-absolute-differences-in-a-sorted-array/
tags:
  - Array
  - Math
  - Prefix Sum
---

# Sum of Absolute Differences in a Sorted Array

## Problem description

Given a sorted integer array `nums`, return an array `result` where `result[i]` is the sum of absolute differences between `nums[i]` and every other element.

## Examples

**Example 1:**

```plaintext
Input: nums = [2, 3, 5]
Output: [4, 3, 5]
Explanation:
  result[0] = |2-3| + |2-5| = 1 + 3 = 4
  result[1] = |3-2| + |3-5| = 1 + 2 = 3
  result[2] = |5-2| + |5-3| = 3 + 2 = 5
```

**Example 2:**

```plaintext
Input: nums = [1, 4, 6, 8, 10]
Output: [24, 15, 13, 15, 21]
```

## Constraints

- `2 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^4`
- `nums` is sorted in non-decreasing order

## Hints

<details>
<summary>Hint 1</summary>

The array is sorted, so you already know the sign of every difference. For index `i`, every element to the left is ≤ `nums[i]` (no absolute value needed — just subtract) and every element to the right is ≥ `nums[i]`. Can you write a closed-form expression for each side without looping?

</details>

<details>
<summary>Hint 2</summary>

The left contribution is `nums[i] * i - sum(nums[0..i-1])`. The right contribution is `sum(nums[i+1..n-1]) - nums[i] * (n - i - 1)`. A running prefix sum gives you both sides in O(1) per index — no prefix array needed.

</details>

## Solution

### Intuition

The brute force computes `sum |nums[i] - nums[j]|` for each `i` by looping over all `j` — O(n²) total. The sorted order removes the absolute value: every element to the left is ≤ `nums[i]`, so those differences are non-negative without the bars.

At index `i`, split the sum into two parts:

```plaintext
left  = sum of (nums[i] - nums[j]) for j < i  =  nums[i] * i  -  prefix
right = sum of (nums[j] - nums[i]) for j > i  =  suffix        -  nums[i] * (n - i - 1)
```

where `prefix` = sum of everything before `i` and `suffix` = sum of everything after `i`. Both are available from a single running total in O(1) per step.

```plaintext
nums = [2, 3, 5],  total = 10

i=0  prefix=0   left = 2*0 - 0 = 0    right = (10-0-2) - 2*2 = 4    result=4
i=1  prefix=2   left = 3*1 - 2 = 1    right = (10-2-3) - 3*1 = 2    result=3
i=2  prefix=5   left = 5*2 - 5 = 5    right = (10-5-5) - 5*0 = 0    result=5
```

### Algorithm

1. Compute `total` = sum of all elements
2. Walk left to right, maintaining a running `prefix` sum (starts at 0)
3. At each index `i`:
   - `left = nums[i] * i - prefix`
   - `right = (total - prefix - nums[i]) - nums[i] * (n - i - 1)`
   - Append `left + right` to the result
   - Add `nums[i]` to `prefix`
4. Return the result

### Complexity analysis

- Time complexity: $O(n)$ — one pass for the total, one pass to build the result
- Space complexity: $O(n)$ — the output array; O(1) auxiliary

```python
from typing import List


class Solution:
    def get_sum_absolute_differences(self, nums: List[int]) -> List[int]:
        n = len(nums)
        total = sum(nums)
        prefix = 0
        result = []
        for i, num in enumerate(nums):
            count_left = i                 # number of elements to the left of i
            sum_left = prefix

            count_right = n - i - 1        # number of elements to the right of i
            sum_right = total - prefix - num

            left_contrib = (num * count_left) - prefix
            right_contrib = sum_right - (num * count_right)

            total_diff = left_contrib + right_contrib
            result.append(total_diff)
       

            prefix += num
       
        return result
```
