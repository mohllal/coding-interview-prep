---
title: Triplets with Smaller Sum
difficulty: Medium
leetcode_title: 3Sum Smaller
leetcode: https://leetcode.com/problems/3sum-smaller/
tags:
  - Array
  - Two Pointers
  - Binary Search
  - Sorting
---

# Triplets with Smaller Sum

## Problem description

Given an array of `n` integers `nums` and an integer `target`, find the number of index triplets `i`, `j`, `k` with `0 <= i < j < k < n` that satisfy the condition `nums[i] + nums[j] + nums[k] < target`.

## Examples

**Example 1:**

```plaintext
Input: nums = [-2,0,1,3], target = 2
Output: 2
Explanation: Because there are two triplets which sums are less than 2:
[-2,0,1]
[-2,0,3]
```

**Example 2:**

```plaintext
Input: nums = [], target = 0
Output: 0
```

**Example 3:**

```plaintext
Input: nums = [0], target = 0
Output: 0
```

## Constraints

- `n == nums.length`
- `0 <= n <= 3500`
- `-100 <= nums[i] <= 100`
- `-100 <= target <= 100`

## Hints

<details>
<summary>Hint 1</summary>

When you find `nums[i] + nums[left] + nums[right] < target`, that is not one triplet — it is several at once.

</details>

<details>
<summary>Hint 2</summary>

Every index between `left` and `right` would also work with that same `left`, because the array is sorted and those values are no larger. So you can count `right - left` triplets in one step.

</details>

## Solution

### Intuition

Similar to 3Sum, but instead of finding exact sums, we count triplets with sums **less than** the target.

Key insight: In a sorted array, if `nums[i] + nums[left] + nums[right] < target`, then **all pairs** between `left` and `right` (with `nums[i]`) also form valid triplets, since replacing `nums[right]` with any smaller element still satisfies the condition.

### Algorithm

1. Sort the array
2. For each index `i`:
   - Early exit if `nums[i] >= target`
   - Count valid pairs using two pointers
   - If `current >= target`: decrement `right`
   - If `current < target`:
     - Add `(right - left)` to count (all pairs between `left` and `right` are valid)
     - Increment `left`
3. Return count

### Complexity analysis

- Time complexity: $O(n^2)$ — Sorting plus nested two-pointer traversal.
- Space complexity: $O(1)$ — Only constant extra space (excluding sorting).

```python
class Solution:
    def three_sum_smaller(self, nums: List[int], target: int) -> int:
        nums.sort()
        n = len(nums)
        count = 0

        for i in range(n - 2):
            left, right = i + 1, n - 1

            while left < right:
                current = nums[i] + nums[left] + nums[right]

                if current < target:
                    # Since `nums[right] >= nums[left]`, we can replace `nums[right]` by any 
                    # number between left and right to get a sum less than the target
                    count += right - left
                    left += 1
                else:
                    right -= 1

        return count
```
