---
title: Number of Good Pairs
difficulty: Easy
leetcode: https://leetcode.com/problems/number-of-good-pairs/
tags:
  - Array
  - Hash Table
  - Math
  - Counting
---

# Number of Good Pairs

## Problem description

Given an array of integers `nums`, return the number of **good pairs**.

A pair `(i, j)` is called *good* if `nums[i] == nums[j]` and `i` < `j`.

## Examples

**Example 1:**

```plaintext
Input: nums = [1,2,3,1,1,3]
Output: 4
Explanation: There are 4 good pairs (0,3), (0,4), (3,4), (2,5) 0-indexed.
```

**Example 2:**

```plaintext
Input: nums = [1,1,1,1]
Output: 6
Explanation: Each pair in the array are good.
```

**Example 3:**

```plaintext
Input: nums = [1,2,3]
Output: 0
```

## Constraints

- `1 <= nums.length <= 100`
- `1 <= nums[i] <= 100`

## Hints

<details>
<summary>Hint 1</summary>

A good pair needs two equal values. If a value appears `c` times, how many pairs does it contribute on its own?

</details>

<details>
<summary>Hint 2</summary>

That is `c * (c - 1) / 2`. You can also accumulate it incrementally: when you meet a value you have already seen `c` times, it forms `c` new pairs right now.

</details>

## Solution

The approach uses a hash map to track how many times each number has been seen so far. For each number, if we've seen it `k` times before, it can form `k` new good pairs with the current occurrence. We add this count to our result and increment the counter for that number.

### Complexity analysis

- Time complexity: $O(n)$ - Single pass through the array.
- Space complexity: $O(n)$ - Hash map to store counts of each unique number.

```python
class Solution:
    def num_identical_pairs(self, nums: List[int]) -> int:
        good_pairs = 0
        counter = {}

        for num in nums:
            if num in counter:
                good_pairs += counter[num]
                counter[num] += 1
            else:
                counter[num] = 1

        return good_pairs
```
