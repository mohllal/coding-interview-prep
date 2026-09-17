---
title: Subarray Sum Equals K
difficulty: Medium
leetcode: https://leetcode.com/problems/subarray-sum-equals-k/
tags:
  - Array
  - Hash Table
  - Prefix Sum
---

# Subarray Sum Equals K

## Problem description

Given an array of integers `nums` and an integer `k`, return the total number of subarrays whose sum equals `k`.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 2, 3], k = 3
Output: 2
Explanation: [1, 2] and [3] both sum to 3
```

**Example 2:**

```plaintext
Input: nums = [10, 2, -2, -20, 10], k = -10
Output: 3
Explanation: [10,2,-2,-20], [2,-2,-20,10], and [-20,10]
```

**Example 3:**

```plaintext
Input: nums = [5, 1, 2, -3, 4, -2], k = 3
Output: 2
Explanation: [2,-3,4] and [1,2]
```

## Constraints

- `1 <= nums.length <= 2 * 10^4`
- `-1000 <= nums[i] <= 1000`
- `-10^7 <= k <= 10^7`

## Hints

<details>
<summary>Hint 1</summary>

A sliding window won't work because the array contains negatives — shrinking the window doesn't reliably reduce the sum. Think about what you need from an earlier position to form a subarray with sum `k` ending at the current position.

</details>

<details>
<summary>Hint 2</summary>

If the running prefix sum at position `j` is `prefix`, then a subarray ending at `j` with sum `k` requires an earlier prefix of `prefix - k`. Keep a frequency map of prefix sums seen so far and add `count[prefix - k]` to the result at each step. Seed the map with `{0: 1}` so subarrays starting at index 0 are counted.

</details>

## Solution

### Intuition

Because the array can contain negatives, growing the window increases the sum unpredictably and shrinking it proves nothing. A sliding window fails.

Instead, use a frequency map of prefix sums. For a subarray `nums[i..j]` to sum to `k`, we need `prefix[j+1] - prefix[i] = k`, i.e., `prefix[i] = prefix[j+1] - k`. The map gives the count of earlier positions with that prefix value in O(1).

```plaintext
nums = [1, 2, 3],  k = 3       count starts as {0: 1}

j   nums[j]  prefix  prefix-k  map lookup  result
0     1        1        -2         0          0
1     2        3         0         1          1
2     3        6         3         1          2

answer = 2
```

The seed `{0: 1}` accounts for subarrays that start at index 0 — at `j=2`, `prefix - k = 3`, found once in the map (from `j=1`), but also at `j=1`, `prefix - k = 0`, found once (the seed), giving the subarray `nums[0..1] = [1, 2]`.

### Algorithm

1. Seed a frequency map with `{0: 1}`
2. Walk left to right, maintaining a running `prefix` sum
3. At each index, add `count[prefix - k]` to the result (defaulting to 0 if absent)
4. Increment `count[prefix]`
5. Return the accumulated result

### Complexity analysis

- Time complexity: $O(n)$ — one pass; each map operation is O(1) amortized
- Space complexity: $O(n)$ — the map holds at most `n + 1` distinct prefix sums

```python
from collections import defaultdict
from typing import List


class Solution:
    def subarray_sum(self, nums: List[int], k: int) -> int:
        count = defaultdict(int)
        count[0] = 1
        prefix = 0
        result = 0
        for num in nums:
            prefix += num
            result += count[prefix - k]
            count[prefix] += 1
        return result
```
