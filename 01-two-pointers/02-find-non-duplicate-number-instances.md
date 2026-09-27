---
title: Find Non-Duplicate Number Instances
difficulty: Easy
leetcode_title: Remove Duplicates from Sorted Array
leetcode: https://leetcode.com/problems/remove-duplicates-from-sorted-array/
tags:
  - Array
  - Two Pointers
---

# Find Non-Duplicate Number Instances

## Problem description

Given an integer array `nums` sorted in non-decreasing order, remove the duplicates in-place such that each unique element appears only once. The **relative order** of the elements should be kept the **same**.

Consider the number of unique elements in `nums` to be `k`. After removing duplicates, return the number of unique elements `k`.

The first `k` elements of `nums` should contain the unique numbers in **sorted order**. The remaining elements beyond index `k - 1` can be ignored.

## Examples

**Example 1:**

```plaintext
Input: nums = [1,1,2]
Output: 2, nums = [1,2,_]
Explanation: Your function should return k = 2, with the first two elements of nums being 1 and 2 respectively.
```

**Example 2:**

```plaintext
Input: nums = [0,0,1,1,1,2,2,3,3,4]
Output: 5, nums = [0,1,2,3,4,_,_,_,_,_]
Explanation: Your function should return k = 5, with the first five elements of nums being 0, 1, 2, 3, and 4 respectively.
```

## Constraints

- `1 <= nums.length <= 3 * 10^4`
- `-100 <= nums[i] <= 100`
- `nums` is sorted in non-decreasing order.

## Hints

<details>
<summary>Hint 1</summary>

The array is sorted, so duplicates are already adjacent. You never have to look further than the previous kept value.

</details>

<details>
<summary>Hint 2</summary>

Keep a write pointer for where the next unique value belongs and a read pointer that scans ahead. They advance at different rates, which is the whole trick.

</details>

## Solution

### Intuition

Since the array is sorted, duplicates are always adjacent. We use two pointers:

- `write`: the position where the next unique element will be placed
- `read`: scans forward through the array

`read` starts at 1 and compares each element to its immediate predecessor. Because the array is sorted, `nums[read] != nums[read - 1]` is exactly the condition for finding a new unique value — no look-back further than one step is ever needed.

```plaintext
arr = [1, 1, 2, 2, 3]

  [1, 1, 2, 2, 3]
      WR                      write=1, read=1

  read=1:  1 == 1  →  duplicate, advance read
  [1, 1, 2, 2, 3]
      W  R

  read=2:  2 != 1  →  unique, copy to write slot, advance both
  [1, 2, 2, 2, 3]
         W  R

  read=3:  2 == 2  →  duplicate, advance read
  [1, 2, 2, 2, 3]
         W     R

  read=4:  3 != 2  →  unique, copy to write slot, write advances
  [1, 2, 3, _, _]
            W                 done (read off end)

  return write = 3
```

### Algorithm

1. Start `write = 1` (index 0 is always kept)
2. Iterate `read` from `1` to `n - 1`:
   - If `nums[read] != nums[read - 1]`, a new unique value was found:
     - Write it to `nums[write]`
     - Advance `write`
3. Return `write` — the count of unique elements, and the length of the valid prefix

### Complexity analysis

- Time complexity: $O(n)$ — Single pass through the array.
- Space complexity: $O(1)$ — In-place modification with two pointers.

```python
class Solution:
    def remove_duplicates(self, nums: List[int]) -> int:
        write = 1
    
        for read in range(1, len(nums)):
            if nums[read] != nums[read - 1]:
                nums[write] = nums[read]
                write += 1
                
        return write
```
