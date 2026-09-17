---
title: Find the Middle Index in Array
difficulty: Easy
leetcode: https://leetcode.com/problems/find-the-middle-index-in-array/
tags:
  - Array
  - Prefix Sum
---

# Find the Middle Index in Array

## Problem description

Given an integer array `nums`, return the leftmost index `i` such that the sum of the elements to the left equals the sum of the elements to the right. If no such index exists, return `-1`.

The left sum of `i == 0` is `0`. The right sum of `i == len(nums) - 1` is `0`.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 7, 3, 6, 5, 6]
Output: 3
Explanation: left sum = 1 + 7 + 3 = 11, right sum = 5 + 6 = 11
```

**Example 2:**

```plaintext
Input: nums = [2, 1, -1]
Output: 0
Explanation: left sum of index 0 = 0, right sum = 1 + -1 = 0
```

**Example 3:**

```plaintext
Input: nums = [2, 3, 5, 5, 3, 2]
Output: -1
Explanation: no index satisfies the condition
```

## Constraints

- `1 <= nums.length <= 100`
- `-1000 <= nums[i] <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

At each index `i`, you want `left_sum == right_sum`. Computing both from scratch every time is O(n) per index. Can you get both in O(1) per index if you know the total sum?

</details>

<details>
<summary>Hint 2</summary>

`right_sum = total - left_sum - nums[i]`. Walk left to right, maintaining a running `left_sum`. At each step, check whether `left_sum == total - left_sum - nums[i]`, then add `nums[i]` to `left_sum` before moving on.

</details>

## Solution

### Intuition

At each index `i`, we need the sum of everything before it and the sum of everything after it. Recomputing both from scratch would be O(n²). Instead, compute the total sum once, then walk left to right with a running `left_sum`. At each position:

```plaintext
right_sum = total - left_sum - nums[i]
```

```plaintext
nums = [1, 7, 3, 6, 5, 6]   total = 28

i=0   left=0    right=28-0-1=27    0 ≠ 27
i=1   left=1    right=28-1-7=20    1 ≠ 20
i=2   left=8    right=28-8-3=17    8 ≠ 17
i=3   left=11   right=28-11-6=11   11 = 11  ✓  return 3
```

### Algorithm

1. Compute the total sum of the array
2. Walk left to right, maintaining `left_sum = 0`
3. At each index `i`, compute `right_sum = total - left_sum - nums[i]`
4. If `left_sum == right_sum`, return `i`
5. Add `nums[i]` to `left_sum` and continue
6. If no index satisfies the condition, return `-1`

### Complexity analysis

- Time complexity: $O(n)$ — one pass for the total, one pass for the scan
- Space complexity: $O(1)$ — only two running variables

```python
from typing import List


class Solution:
    def find_middle_index(self, nums: List[int]) -> int:
        total = sum(nums)
        left_sum = 0
        for i, num in enumerate(nums):
            right_sum = total - left_sum - num
            if left_sum == right_sum:
                return i
            left_sum += num
        return -1
```
