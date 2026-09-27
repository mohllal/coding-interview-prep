---
title: Triplet Sum Close to Target
difficulty: Medium
leetcode_title: 3Sum Closest
leetcode: https://leetcode.com/problems/3sum-closest/
tags:
  - Array
  - Two Pointers
  - Sorting
---

# Triplet Sum Close to Target

## Problem description

Given an integer array `nums` of length `n` and an integer `target`, find three integers in `nums` such that the sum is closest to `target`.

Return the sum of the three integers.

You may assume that each input would have exactly one solution.

## Examples

**Example 1:**

```plaintext
Input: nums = [-1,2,1,-4], target = 1
Output: 2
Explanation: The sum that is closest to the target is 2. (-1 + 2 + 1 = 2).
```

**Example 2:**

```plaintext
Input: nums = [0,0,0], target = 1
Output: 0
Explanation: The sum that is closest to the target is 0. (0 + 0 + 0 = 0).
```

## Constraints

- `3 <= nums.length <= 500`
- `-1000 <= nums[i] <= 1000`
- `-10^4 <= target <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

Same shape as finding a triplet that sums to zero, but nothing has to match exactly — you are minimising a distance instead.

</details>

<details>
<summary>Hint 2</summary>

Track the best difference seen so far. The sign of `current_sum - target` still tells you which pointer to move, exactly as if you were searching for an exact hit.

</details>

## Solution

### Intuition

Similar to 3Sum, but instead of finding exact matches, we track the closest sum. For each element, use two pointers to search for a pair that minimizes the distance to `target`.

At each step:

- If `current_sum < target`: move `left` right to increase the sum
- If `current_sum > target`: move `right` left to decrease the sum
- If `current_sum == target`: return immediately (can't get closer than 0)

When two sums have the same distance, prefer the smaller sum.

### Algorithm

1. Sort the array
2. Initialize `closest_sum` with infinity distance
3. For each index `i`:
   - Use two pointers (`left`, `right`) to find pairs
   - Update `closest_sum` if current sum is closer, or same distance but smaller
   - Move pointers based on whether current sum is less or greater than target
4. Return `closest_sum`

### Complexity analysis

- Time complexity: $O(n^2)$ — Sorting plus nested two-pointer search.
- Space complexity: $O(1)$ — Only constant extra space (excluding sorting).

```python
class Solution:
    def three_sum_closest(self, nums: List[int], target: int) -> int:
        nums.sort()
        n = len(nums)

        closest = nums[0] + nums[1] + nums[2]

        for i in range(n - 2):
            left, right = i + 1, n - 1

            while left < right:
                current = nums[i] + nums[left] + nums[right]

                # Update the closest sum 
                current_distance = abs(current - target)
                closest_distance = abs(closest - target) 

                if current_distance < closest_distance:
                    closest = current

                if current < target:
                    left += 1
                elif current > target:
                    right -= 1
                else:
                    return current  # exact match

        return closest
```
