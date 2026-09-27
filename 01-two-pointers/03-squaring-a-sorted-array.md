---
title: Squaring a Sorted Array
difficulty: Easy
leetcode_title: Squares of a Sorted Array
leetcode: https://leetcode.com/problems/squares-of-a-sorted-array/
tags:
  - Array
  - Two Pointers
  - Sorting
---

# Squaring a Sorted Array

## Problem description

Given an integer array `nums` sorted in non-decreasing order, return an array of **the squares of each number** sorted in non-decreasing order.

## Examples

**Example 1:**

```plaintext
Input: nums = [-4,-1,0,3,10]
Output: [0,1,9,16,100]
Explanation: After squaring, the array becomes [16,1,0,9,100].
After sorting, it becomes [0,1,9,16,100].
```

**Example 2:**

```plaintext
Input: nums = [-7,-3,2,3,11]
Output: [4,9,9,49,121]
```

## Constraints

- `1 <= nums.length <= 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `nums` is sorted in non-decreasing order.

**Follow up:** Squaring each element and sorting the new array is very trivial, could you find an $O(n)$ solution using a different approach?

## Hints

<details>
<summary>Hint 1</summary>

Squaring destroys the sorted order, because a large negative becomes a large positive. Where in the array do the *biggest* squares live?

</details>

<details>
<summary>Hint 2</summary>

At the two ends. Compare the magnitudes at each end and fill the output from the back forwards.

</details>

## Solution

### Intuition

The array contains negative numbers, and squaring them can produce large values. Key insight: **the largest squares are at the extremes** (far left for large negative numbers, far right for large positive numbers).

By using two pointers from both ends, we can build the result array from largest to smallest, then reverse it.

### Algorithm

1. Initialize `left = 0` and `right = len(nums) - 1`
2. Initialize empty `squares` list
3. While `left <= right`:
   - Compare `abs(nums[left])` with `abs(nums[right])`
   - Append the larger square to `squares`
   - Move the corresponding pointer inward
4. Reverse `squares` to get ascending order

### Complexity analysis

- Time complexity: $O(n)$ — Single pass with two pointers, plus $O(n)$ for reverse.
- Space complexity: $O(n)$ — For the result array.

```python
class Solution:
    def sorted_squares(self, nums: List[int]) -> List[int]:
        n = len(nums)
        squares = [0] * n

        left, right = 0, n - 1
        pos = n - 1

        while left <= right:
            left_sq = nums[left] * nums[left]
            right_sq = nums[right] * nums[right]

            if left_sq > right_sq:
                squares[pos] = left_sq
                left += 1
            else:
                squares[pos] = right_sq
                right -= 1

            pos -= 1

        return squares
```
