---
title: Maximum Size Subarray Sum Equals k
difficulty: Medium
leetcode: https://leetcode.com/problems/maximum-size-subarray-sum-equals-k/
tags:
  - Array
  - Hash Table
  - Prefix Sum
---

# Maximum Size Subarray Sum Equals k

## Problem description

Given an array of integers `nums` and an integer `k`, return the length of the longest subarray that sums to `k`. If no such subarray exists, return `0`.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 2, 3, -2, 5], k = 5
Output: 2
Explanation: [2, 3] sums to 5 and has length 2
```

**Example 2:**

```plaintext
Input: nums = [-2, -1, 2, 1], k = 1
Output: 2
Explanation: [-1, 2] sums to 1 and has length 2
```

**Example 3:**

```plaintext
Input: nums = [3, 4, 7, 2, -3, 1, 4, 2], k = 7
Output: 4
Explanation: [7, 2, -3, 1] sums to 7 and has length 4
```

## Constraints

- `1 <= nums.length <= 2 * 10^5`
- `-10^4 <= nums[i] <= 10^4`
- `-10^9 <= k <= 10^9`

## Hints

<details>
<summary>Hint 1</summary>

Because the array contains negatives, a sliding window won't work — shrinking the window doesn't reliably decrease the sum. Think about what you know at each position and what you'd need from an earlier position to form a subarray with sum `k`.

</details>

<details>
<summary>Hint 2</summary>

If `prefix[j] - prefix[i] = k`, then the subarray from `i` to `j-1` sums to `k`. At each index `j`, look up `prefix[j] - k` in a hash map. Store the first (earliest) occurrence of each prefix sum — not the latest — so the discovered subarray is as long as possible.

</details>

## Solution

### Intuition

A sliding window fails here because the array contains negatives: growing the window can decrease the sum, so shrinking proves nothing.

The prefix-sum approach works instead. Let `prefix[j]` = sum of the first `j` elements. If `prefix[j] - prefix[i] = k`, the subarray `nums[i..j-1]` sums to `k` and has length `j - i`.

At each position `j`, look up `prefix[j] - k` in a hash map that records where each prefix sum was first seen. If it is there, compute the length and update the maximum.

```plaintext
nums = [1, 2, 3, -2, 5],  k = 5       map starts as {0: -1}

j   prefix   prefix-k   map              action         max_len
0    1          -4       not found        record 1→0       0
1    3          -2       not found        record 3→1       0
2    6           1       found at 0       len = 2-0 = 2    2
3    4          -1       not found        record 4→3       2
4    9           4       found at 3       len = 4-3 = 1    2

answer = 2
```

We record a prefix sum only if it is not already in the map, because the earliest occurrence gives the longest subarray.

### Algorithm

1. Seed the map with `{0: -1}` (prefix sum of 0 seen before index 0)
2. Walk left to right, maintaining a running `prefix` sum
3. At each index `j`, if `prefix - k` is in the map, update `max_len` with `j - map[prefix - k]`
4. If `prefix` is not yet in the map, record `map[prefix] = j`
5. Return `max_len`

### Complexity analysis

- Time complexity: $O(n)$ — one pass; each hash map operation is O(1) amortized
- Space complexity: $O(n)$ — the map holds at most `n` distinct prefix sums

```python
from typing import List


class Solution:
    def max_sub_array_len(self, nums: List[int], k: int) -> int:
        # Initialize with {0: -1} so that if nums[0] == k (i.e., the subarray starts at index 0), 
        # the difference prefix- k will be found at index -1 and max_len = 0 - (-1) = 1
        prefix_index = {0: -1}
   
        prefix = 0
        max_len = 0
        for j, num in enumerate(nums):
            prefix += num
            if prefix - k in prefix_index:
                max_len = max(max_len, j - prefix_index[prefix - k])
            if prefix not in prefix_index:
                prefix_index[prefix] = j
        return max_len
```
