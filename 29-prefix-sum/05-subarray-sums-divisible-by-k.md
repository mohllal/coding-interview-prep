---
title: Subarray Sums Divisible by K
difficulty: Medium
leetcode: https://leetcode.com/problems/subarray-sums-divisible-by-k/
tags:
  - Array
  - Hash Table
  - Prefix Sum
---

# Subarray Sums Divisible by K

## Problem description

Given an integer array `nums` and an integer `k`, return the number of non-empty subarrays that have a sum divisible by `k`.

## Examples

**Example 1:**

```plaintext
Input: nums = [3, 1, 2, -2, 5, -1], k = 3
Output: 7
Explanation: [3], [1,2], [3,1,2], [3,1,2,-2,5], [1,2,-2,5], [-2,5], [2,-2]
```

**Example 2:**

```plaintext
Input: nums = [4, 5, 0, -2, -3, 1], k = 5
Output: 7
Explanation: [5], [4,5,0,-2,-3,1], [5,0], [0], [5,0,-2,-3], [0,-2,-3], [-2,-3]
```

**Example 3:**

```plaintext
Input: nums = [-1, 2, 9], k = 2
Output: 2
Explanation: [2] and [-1, 2, 9]
```

## Constraints

- `1 <= nums.length <= 3 * 10^4`
- `-10^4 <= nums[i] <= 10^4`
- `2 <= k <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

A subarray sum is divisible by `k` if and only if the two prefix sums bounding it have the same remainder when divided by `k`. Why? Because `prefix[j] - prefix[i]` divisible by `k` means `prefix[j] % k == prefix[i] % k`.

</details>

<details>
<summary>Hint 2</summary>

Maintain a running prefix sum modulo `k` and a frequency map of remainders seen so far. For each new remainder `r`, the number of valid subarrays ending here is `count[r]` — the number of earlier positions with the same remainder. Seed the map with `{0: 1}` to handle subarrays starting at index 0.

</details>

## Solution

### Intuition

Two prefix sums have the same remainder mod `k` if and only if their difference is divisible by `k`. So instead of checking every pair, we maintain a running prefix sum mod `k` and count how many times each remainder has appeared.

```plaintext
nums = [4, 5, 0, -2, -3, 1],  k = 5       count starts as {0: 1}

j   nums[j]  prefix  prefix%k  count added  result
0     4        4        4            0         0
1     5        9        4            1         1
2     0        9        4            2         3
3    -2        7        2            0         3
4    -3        4        4            3         6
5     1        5        0            1         7

answer = 7
```

At `j=1`, `prefix % k = 4`, and `count[4] = 1` (from `j=0`), giving one new subarray: `nums[0..1] = [4, 5]`, sum = 9, divisible by 5? No — wait, let me re-examine.

Actually `[4, 5]` sums to 9, not divisible by 5. The matching remainders at `j=0` (prefix=4, r=4) and `j=1` (prefix=9, r=4) mean `9 - 4 = 5`, which is divisible by 5. That subarray is `nums[1..1] = [5]`.

The key invariant: when two positions share a remainder, the subarray between them has a sum divisible by `k`.

### Algorithm

1. Seed a frequency map with `{0: 1}` (the empty prefix has remainder 0)
2. Walk left to right, maintaining a running prefix sum
3. At each index, compute `remainder = prefix % k`
4. Add `count[remainder]` to the result (every earlier matching remainder closes a valid subarray)
5. Increment `count[remainder]`
6. Return the accumulated result

### Complexity analysis

- Time complexity: $O(n)$ — one pass; each map operation is O(1) amortized
- Space complexity: $O(k)$ — the map holds at most `k` distinct remainders

```python
from collections import defaultdict
from typing import List


class Solution:
    def subarrays_div_by_k(self, nums: List[int], k: int) -> int:
        count = defaultdict(int)
        count[0] = 1
        prefix = 0
        result = 0
        for num in nums:
            prefix = (prefix + num) % k
            result += count[prefix]
            count[prefix] += 1
        return result
```
