---
title: Dutch National Flag Problem
difficulty: Medium
leetcode_title: Sort Colors
leetcode: https://leetcode.com/problems/sort-colors/
tags:
  - Array
  - Two Pointers
  - Sorting
  - Quicksort
  - Bubble Sort
---

# Dutch National Flag Problem

## Problem description

Given an array `nums` with `n` objects colored red, white, or blue, sort them in-place so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

## Examples

**Example 1:**

```plaintext
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]
```

**Example 2:**

```plaintext
Input: nums = [2,0,1]
Output: [0,1,2]
```

## Constraints

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` is either `0`, `1`, or `2`.

**Follow up:** Could you come up with a one-pass algorithm using only constant extra space?

## Hints

<details>
<summary>Hint 1</summary>

Counting each colour and rewriting the array works but takes two passes. Can you do it in one, in place?

</details>

<details>
<summary>Hint 2</summary>

Keep three regions: settled `0`s at the front, settled `2`s at the back, and the unexamined middle. After swapping a `2` into place, be careful about whether the current index has really been resolved.

</details>

## Solution 1: Two passes

### Intuition

Handle one color at a time. The first pass sweeps all 0s to the front using a write pointer, exactly like removing duplicates.

The second pass sweeps all 2s to the back the same way but from the right — and it only needs to look at the portion that wasn't already claimed by the first zeros pass (indices `write` onward).

After both passes, the 1s sit in the middle automatically — there is nowhere else for them to be.

### Algorithm

1. Pass 1: walk `i` left-to-right; whenever `nums[i] == 0`, swap it to `low` and advance `low`
2. Pass 2: walk `i` right-to-left from `n-1` down to `low`; whenever `nums[i] == 2`, swap it to `high` and retreat `high`

### Complexity analysis

- Time complexity: $O(n)$ — two linear passes
- Space complexity: $O(1)$ — two write pointers

```python
class Solution:
    def sort_colors(self, nums: List[int]) -> None:
        n = len(nums)

        # pass 1: push all 0s to the front
        low = 0
        for i in range(n):
            if nums[i] == 0:
                nums[low], nums[i] = nums[i], nums[low]
                low += 1

        # pass 2: push all 2s to the back, starting just after the settled 0s
        high = n - 1
        for i in range(n - 1, low - 1, -1):
            if nums[i] == 2:
                nums[high], nums[i] = nums[i], nums[high]
                high -= 1
```

## Solution 2: One pass

### Intuition

We use three variables: `low` (next position to place a 0), `high` (next position to place a 2, from the right end), and `i` to iterate through the array.

When `i` hits a 0, swap it to `low` and move both forward. When it hits a 2, swap it to `high` and move `high` back — but leave `i` where it is, because the element that just arrived from `high` has not been examined yet. When it hits a 1, just move `i` forward.

```plaintext
arr = [2, 0, 2, 1, 1, 0]

  [2, 0, 2, 1, 1, 0]
   L              H      low=0, i=0, high=5
   i

  nums[i]=2 → swap i↔high, high--  (don't advance i — new element unseen)
  [0, 0, 2, 1, 1, 2]
   L           H         low=0, i=0, high=4
   i

  nums[i]=0 → swap i↔low, low++, i++
  [0, 0, 2, 1, 1, 2]
      L        H         low=1, i=1, high=4
      i

  nums[i]=0 → swap i↔low, low++, i++
  [0, 0, 2, 1, 1, 2]
         L    H          low=2, i=2, high=4
         i

  nums[i]=2 → swap i↔high, high--
  [0, 0, 1, 1, 2, 2]
         L  H            low=2, i=2, high=3
         i

  nums[i]=1 → i++
  [0, 0, 1, 1, 2, 2]
         L  i            low=2, i=3, high=3
            H

  nums[i]=1 → i++  (i=4 > high=3, done)
  [0, 0, 1, 1, 2, 2]  ✓
```

### Algorithm

1. Set `low = 0`, `i = 0`, `high = n - 1`
2. While `i <= high`:
   - `nums[i] == 0` → swap `i` and `low`, advance both
   - `nums[i] == 2` → swap `i` and `high`, shrink `high` only
   - `nums[i] == 1` → advance `i`

### Complexity analysis

- Time complexity: $O(n)$ — `i` advances on every step except when swapping a 2 (where `high` shrinks instead). Together they make at most $n$ moves.
- Space complexity: $O(1)$ — three pointers, no extra memory.

```python
class Solution:
    def sort_colors(self, nums: List[int]) -> None:
        low, i, high = 0, 0, len(nums) - 1

        while i <= high:
            if nums[i] == 0:
                nums[i], nums[low] = nums[low], nums[i]
                low += 1
                i += 1
            elif nums[i] == 2:
                nums[i], nums[high] = nums[high], nums[i]
                high -= 1
            else:
                i += 1
```
