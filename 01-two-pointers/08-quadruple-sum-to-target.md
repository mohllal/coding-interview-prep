---
title: Quadruple Sum to Target
difficulty: Medium
leetcode_title: 4Sum
leetcode: https://leetcode.com/problems/4sum/
tags:
  - Array
  - Two Pointers
  - Sorting
---

# Quadruple Sum to Target

## Problem description

Given an array `nums` of `n` integers, return an array of all the unique quadruplets `[nums[a], nums[b], nums[c], nums[d]]` such that:

- `0 <= a, b, c, d < n`
- `a`, `b`, `c`, and `d` are distinct.
- `nums[a] + nums[b] + nums[c] + nums[d] == target`

You may return the answer in **any order**.

## Examples

**Example 1:**

```plaintext
Input: nums = [1,0,-1,0,-2,2], target = 0
Output: [[-2,-1,1,2],[-2,0,0,2],[-1,0,0,1]]
```

**Example 2:**

```plaintext
Input: nums = [2,2,2,2,2], target = 8
Output: [[2,2,2,2]]
```

## Constraints

- `1 <= nums.length <= 200`
- `-10^9 <= nums[i] <= 10^9`
- `-10^9 <= target <= 10^9`

## Hints

<details>
<summary>Hint 1</summary>

This is the triplet problem with one more layer. Fix two numbers and the remainder is a two-pointer search.

</details>

<details>
<summary>Hint 2</summary>

Sort first. Skipping duplicates now matters at *every* level — both fixed indices and both moving pointers.

</details>

## Solution

### Intuition

Extends the [3Sum](./04-triplet-sum-to-zero.md) approach with an additional outer loop. Two nested loops fix the first two elements, then use two pointers to find the remaining pair.

Skip duplicates at each level to avoid duplicate quadruplets.

### Algorithm

1. Sort the array
2. For each index `i`:
   - Skip duplicates
   - For each index `j > i`:
     - Skip duplicates
     - Use two pointers (`left`, `right`) to find pairs where sum equals `target - nums[i] - nums[j]`
     - When found, add quadruplet and skip duplicates for both pointers
3. Return all quadruplets

### Complexity analysis

- Time complexity: $O(n^3)$ — Two nested loops plus two-pointer search.
- Space complexity: $O(n^3)$

#### Why space complexity is $O(n^3)$?

When including the output, the result list has **$O(n^3)$** space complexity due to the number of valid quadruplets it may store.

- The algorithm itself uses only constant extra space (pointers and loop variables).
- Two indices (`i`, `j`) are fixed first, giving $O(n^2)$ combinations.
- For each `(i, j)` pair, there can be up to $O(n)$ valid `(left, right)` pairs.
- Each valid combination produces a distinct quadruplet that must be stored.

Therefore, the total number of stored quadruplets in the worst case is:

$O(n^2) \times O(n) = O(n^3)$

```python
class Solution:
    def four_sum(self, nums: List[int], target: int) -> List[List[int]]:
        nums.sort()
        quadruplets = []

        n = len(nums)

        for i in range(n - 3):
            if i > 0 and nums[i] == nums[i - 1]:
                continue

            for j in range(i + 1, n - 2):
                if j > i + 1 and nums[j] == nums[j - 1]:
                    continue

                left, right = j + 1, n - 1

                while left < right:
                    total = nums[i] + nums[j] + nums[left] + nums[right]

                    if total < target:
                        left += 1
                    elif total > target:
                        right -= 1
                    else:
                        quadruplets.append([nums[i], nums[j], nums[left], nums[right]])
                        
                        # Skip duplicate values for left pointer
                        while left < right and nums[left] == nums[left + 1]:
                            left += 1
                            
                        # Skip duplicate values for right pointer
                        while left < right and nums[right] == nums[right - 1]:
                            right -= 1

                        left += 1
                        right -= 1
        
        return quadruplets
```
