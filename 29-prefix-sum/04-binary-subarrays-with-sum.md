---
title: Binary Subarrays With Sum
difficulty: Medium
leetcode: https://leetcode.com/problems/binary-subarrays-with-sum/
tags:
  - Array
  - Hash Table
  - Sliding Window
  - Prefix Sum
---

# Binary Subarrays With Sum

## Problem description

Given a binary array `nums` and an integer `goal`, return the number of non-empty subarrays with sum equal to `goal`.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 1, 0, 1, 1], goal = 2
Output: 5
Explanation: [1,1], [1,1,0], [1,0,1], [0,1,1], [1,1]
```

**Example 2:**

```plaintext
Input: nums = [1, 1, 1, 1, 0, 0], goal = 3
Output: 4
Explanation: [1,1,1], [1,1,1], [1,1,1,0], [1,1,1,0,0]
```

**Example 3:**

```plaintext
Input: nums = [0, 0, 0, 0, 1, 0, 1], goal = 1
Output: 12
```

## Constraints

- `1 <= nums.length <= 3 * 10^4`
- `nums[i]` is either `0` or `1`
- `0 <= goal <= nums.length`

## Hints

<details>
<summary>Hint 1</summary>

At each position you might close many valid subarrays at once. Think about what you need from earlier positions to count all of them in one step, rather than searching backward each time.

</details>

<details>
<summary>Hint 2</summary>

Keep a frequency map of prefix sums seen so far. When the current prefix sum is `p`, the number of subarrays ending here with sum `goal` equals `count[p - goal]` — every earlier prefix value of `p - goal` starts a valid subarray ending at the current position.

</details>

## Solution

### Intuition

This is a counting problem: at each index, we want to know how many earlier positions form a valid subarray with the current index as the right endpoint.

The prefix-sum technique turns that into a hash map lookup. Let `prefix` be the running sum at the current index. A subarray ending here has sum `goal` if its left boundary `i` satisfies `prefix[right+1] - prefix[i] = goal`, i.e., `prefix[i] = prefix - goal`. We want the count of earlier positions with that prefix value, which the map gives in O(1).

```plaintext
nums = [1, 1, 0, 1, 1],  goal = 2       count starts as {0: 1}

j   nums[j]  prefix  prefix-goal  count added  result
0     1        1         -1            0            0
1     1        2          0            1            1
2     0        2          0            1            2
3     1        3          1            1            3
4     1        4          2            2            5

answer = 5
```

The seed `{0: 1}` accounts for subarrays starting at index 0.

### Algorithm

1. Seed a frequency map with `{0: 1}`
2. Walk left to right, maintaining a running `prefix` sum
3. At each index, add `count[prefix - goal]` to the result (defaulting to 0 if absent)
4. Increment `count[prefix]`
5. Return the accumulated result

### Complexity analysis

- Time complexity: $O(n)$ — one pass; each map operation is O(1) amortized
- Space complexity: $O(n)$ — the map holds at most `n + 1` distinct prefix sums

```python
from collections import defaultdict
from typing import List


class Solution:
    def num_subarrays_with_sum(self, nums: List[int], goal: int) -> int:
        # Initializing count[0] = 1 ensures we count subarrays starting at index 0.
        # If, at the first element, prefix == goal, then prefix - goal == 0,
        # and count[0] (which starts at 1) means we count the subarray nums[0:1].
        count = defaultdict(int)
        count[0] = 1
        prefix = 0
        result = 0
        for num in nums:
            prefix += num
            result += count[prefix - goal]
            count[prefix] += 1
        return result
```
