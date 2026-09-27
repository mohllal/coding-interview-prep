---
title: Pair with Target Sum
difficulty: Medium
leetcode_title: Two Sum II - Input Array Is Sorted
leetcode: https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/
tags:
  - Array
  - Two Pointers
  - Binary Search
---

# Pair with Target Sum

## Problem description

Given a **1-indexed** array of integers `numbers` that is already **sorted in non-decreasing order**, find two numbers such that they add up to a specific `target` number. Let these two numbers be `numbers[index1]` and `numbers[index2]` where `1 <= index1 < index2 <= numbers.length`.

Return the indices of the two numbers, `index1` and `index2`, **added by one** as an integer array `[index1, index2]` of length 2.

The tests are generated such that there is exactly one solution. You **may not** use the same element twice.

Your solution must use only constant extra space.

## Examples

**Example 1:**

```plaintext
Input: numbers = [2,7,11,15], target = 9
Output: [1,2]
Explanation: The sum of 2 and 7 is 9. Therefore, index1 = 1, index2 = 2. We return [1, 2].
```

**Example 2:**

```plaintext
Input: numbers = [2,3,4], target = 6
Output: [1,3]
Explanation: The sum of 2 and 4 is 6. Therefore index1 = 1, index2 = 3. We return [1, 3].
```

**Example 3:**

```plaintext
Input: numbers = [-1,0], target = -1
Output: [1,2]
Explanation: The sum of -1 and 0 is -1. Therefore index1 = 1, index2 = 2. We return [1, 2].
```

## Constraints

- `2 <= numbers.length <= 3 * 10^4`
- `-1000 <= numbers[i] <= 1000`
- `numbers` is sorted in non-decreasing order.
- `-1000 <= target <= 1000`
- The tests are generated such that there is exactly one solution.

## Hints

<details>
<summary>Hint 1</summary>

A hash map solves this in $O(n)$ time and $O(n)$ space. The array is sorted, though — can you get the space down to $O(1)$?

</details>

<details>
<summary>Hint 2</summary>

Start at both ends. The sum of the two ends tells you which pointer to move: too small means you need a bigger number, too large means a smaller one.

</details>

## Solution

### Intuition

Since the array is sorted, we can use two pointers starting from opposite ends. The sum of elements at these pointers tells us which direction to move:

- **Sum too small** → move left pointer right (get a larger number)
- **Sum too large** → move right pointer left (get a smaller number)
- **Sum equals target** → found our pair

### Algorithm

1. Initialize `left` pointer at index `0` and `right` pointer at the last index
2. While `left < right`:
   - Calculate `current_sum = numbers[left] + numbers[right]`
   - If `current_sum < target`: increment `left` (need larger sum)
   - If `current_sum > target`: decrement `right` (need smaller sum)
   - If `current_sum == target`: return `[left + 1, right + 1]` (1-indexed)
3. Return `[-1, -1]` if no pair found

### Complexity analysis

- Time complexity: $O(n)$ — Each pointer moves at most n times.
- Space complexity: $O(1)$ — Only two pointers used.

```python
class Solution:
    def two_sum(self, numbers: List[int], target: int) -> List[int]:
        left = 0
        right = len(numbers) - 1

        while left < right:
            current_sum = numbers[left] + numbers[right]
            if current_sum < target:
                left += 1
            elif current_sum > target:
                right -= 1
            else:
                return [left + 1, right + 1]

        return [-1, -1]
```
