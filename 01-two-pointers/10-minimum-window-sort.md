---
title: Minimum Window Sort
difficulty: Medium
leetcode_title: Shortest Unsorted Continuous Subarray
leetcode: https://leetcode.com/problems/shortest-unsorted-continuous-subarray/
tags:
  - Array
  - Two Pointers
  - Stack
  - Greedy
  - Sorting
  - Monotonic Stack
---

# Minimum Window Sort

## Problem description

Given an integer array `nums`, you need to find one **continuous subarray** such that if you only sort this subarray in non-decreasing order, then the whole array will be sorted in non-decreasing order.

Return the shortest such subarray and output its length.

## Examples

**Example 1:**

```plaintext
Input: nums = [2,6,4,8,10,9,15]
Output: 5
Explanation: You need to sort [6, 4, 8, 10, 9] in ascending order to make the whole array sorted in ascending order.
```

**Example 2:**

```plaintext
Input: nums = [1,2,3,4]
Output: 0
```

**Example 3:**

```plaintext
Input: nums = [1]
Output: 0
```

## Constraints

- `1 <= nums.length <= 10^4`
- `-10^5 <= nums[i] <= 10^5`

**Follow up:** Can you solve it in `O(n)` time complexity?

## Hints

<details>
<summary>Hint 1</summary>

Sorting a copy and comparing gives the answer in $O(n \log n)$. To do better, find the two boundaries directly.

</details>

<details>
<summary>Hint 2</summary>

Scan inward from both ends to find the first out-of-order element on each side. Then widen the window to swallow any value outside it that falls inside the window's min and max.

</details>

## Solution

### Intuition

Finding the first out-of-order elements from both ends gives us a candidate subarray. But this might not be enough — we need to extend the subarray to include:

- Any element before the subarray that's greater than the subarray's minimum
- Any element after the subarray that's less than the subarray's maximum

### Algorithm

1. Find `left`: first index where `nums[left] > nums[left + 1]` (from start)
2. Find `right`: first index where `nums[right] < nums[right - 1]` (from end)
3. If `left >= right`: array is already sorted, return 0
4. Find `min` and `max` values in the subarray `[left, right]`
5. Extend `left` leftward while `nums[left-1] > min`
6. Extend `right` rightward while `nums[right+1] < max`
7. Return `right - left + 1`

### Complexity analysis

- Time complexity: $O(n)$ — Multiple linear passes.
- Space complexity: $O(1)$ — Only pointers and min/max values.

```python
class Solution:
    def find_unsorted_subarray_window(self, nums: List[int]) -> Tuple[int, int]:
        # Find first index from the left where order breaks
        left = 0
        while left < len(nums) - 1 and nums[left] <= nums[left + 1]:
            left += 1

        # Find first index from the right where order breaks
        right = len(nums) - 1
        while right > 0 and nums[right] >= nums[right - 1]:
            right -= 1

        return left, right

    def find_unsorted_subarray(self, nums: List[int]) -> int:
        left, right = self.find_unsorted_subarray_window(nums)

        # If the array is already sorted
        if left >= right:
            return 0

        # Find min and max inside the initial unsorted window
        minimum = min(nums[left:right + 1])
        maximum = max(nums[left:right + 1])

        # Expand left boundary to find true start: first element before the window that is greater than the minimum inside it
        while left > 0 and nums[left - 1] > minimum:
            left -= 1

        # Expand right boundary to find true end: first element after the window that is smaller than the maximum inside it,
        while right < len(nums) - 1 and nums[right + 1] < maximum:
            right += 1

        return end - start + 1
```
