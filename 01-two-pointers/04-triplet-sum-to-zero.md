---
title: Triplet Sum to Zero
difficulty: Medium
leetcode_title: 3Sum
leetcode: https://leetcode.com/problems/3sum/
tags:
  - Array
  - Two Pointers
  - Sorting
---

# Triplet Sum to Zero

## Problem description

Given an integer array `nums`, return all the triplets `[nums[i], nums[j], nums[k]]` such that `i != j`, `i != k`, and `j != k`, and `nums[i] + nums[j] + nums[k] == 0`.

Notice that the solution set must not contain duplicate triplets.

## Examples

**Example 1:**

```plaintext
Input: nums = [-1,0,1,2,-1,-4]
Output: [[-1,-1,2],[-1,0,1]]
Explanation: 
nums[0] + nums[1] + nums[2] = (-1) + 0 + 1 = 0.
nums[1] + nums[2] + nums[4] = 0 + 1 + (-1) = 0.
nums[0] + nums[3] + nums[4] = (-1) + 2 + (-1) = 0.
The distinct triplets are [-1,0,1] and [-1,-1,2].
```

**Example 2:**

```plaintext
Input: nums = [0,1,1]
Output: []
Explanation: The only possible triplet does not sum up to 0.
```

**Example 3:**

```plaintext
Input: nums = [0,0,0]
Output: [[0,0,0]]
Explanation: The only possible triplet sums up to 0.
```

## Constraints

- `3 <= nums.length <= 3000`
- `-10^5 <= nums[i] <= 10^5`

## Hints

<details>
<summary>Hint 1</summary>

Fix one number and the problem becomes: find two numbers in the rest that sum to a known target. You have already solved that one.

</details>

<details>
<summary>Hint 2</summary>

Sort first, so the inner search can be two converging pointers. Then think hard about how to avoid emitting the same triplet twice.

</details>

## Solution

### Intuition

Reduce the 3Sum problem to multiple 2Sum problems. For each element `X`, find pairs `(Y, Z)` where `Y + Z = -X`. Sorting enables the two-pointer technique and helps skip duplicates.

### Algorithm

1. Sort the array
2. For each index `i`:
   - Skip if `nums[i] == nums[i-1]` (avoid duplicate triplets)
   - Set `target = -nums[i]`
   - Use two pointers (`left`, `right`) to find pairs summing to `target`
   - When a valid pair is found:
     - Add triplet to result
     - Skip duplicate values for both pointers
3. Return all triplets

### Complexity analysis

- Time complexity: $O(n^2)$ — Sorting is $O(n \log n)$, then for each element we do a linear scan.
- Space complexity: $O(n)$ — For sorting (depending on implementation) and storing results.

```python
class Solution:
    def three_sum(self, nums: List[int]) -> List[List[int]]:
        nums.sort()
        triplets = []

        n = len(nums)

        for i in range(n - 2):
            # Skip duplicate first elements
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            left, right = i + 1, n - 1

            while left < right:
                total = nums[i] + nums[left] + nums[right]

                if total < 0:
                    left += 1
                elif total > 0:
                    right -= 1
                else:
                    triplets.append([nums[i], nums[left], nums[right]])

                    # Skip duplicates for left pointer
                    while left < right and nums[left] == nums[left + 1]:
                        left += 1

                    # Skip duplicates for right pointer
                    while left < right and nums[right] == nums[right - 1]:
                        right -= 1

                    left += 1
                    right -= 1

        return triplets
```
