---
title: Largest Unique Number
difficulty: Easy
leetcode_title: Largest Unique Number
leetcode: https://leetcode.com/problems/largest-unique-number/
tags:
  - Array
  - Hash Table
  - Sorting
---

# Largest Unique Number

## Problem description

Given an array of integers, identify the highest value that appears only once. If no such value exists, return `-1`.

## Examples

**Example 1:**

```plaintext
Input:  [5, 7, 3, 7, 5, 8]
Output: 8

8 is the only element that appears once, and it is the largest.
```

**Example 2:**

```plaintext
Input:  [1, 2, 3, 2, 1, 4, 4]
Output: 3

3 is the only element appearing once. 1, 2, and 4 all appear twice.
```

**Example 3:**

```plaintext
Input:  [9, 9, 8, 8, 7, 7]
Output: -1

All elements appear more than once.
```

## Constraints

- `0 <= A.length <= 2000`
- `0 <= A[i] <= 1000`

## Hints

<details>
<summary>Hint 1</summary>

Build a frequency map first. Then decide which unique value to return — think about what order you'd need to scan to find the *largest* one.

</details>

<details>
<summary>Hint 2</summary>

You can filter the unique elements and take the max, or scan from the largest value downward. Both are linear after the frequency map is built.

</details>

## Solution

### Intuition

Count frequencies, then find the maximum value among those with count exactly 1.

```plaintext
A = [5, 7, 3, 7, 5, 8]

Frequency map:
  5 → 2
  7 → 2
  3 → 1
  8 → 1

Unique values: [3, 8]
Max of unique: 8
```

### Algorithm

1. Build a frequency map of all elements
2. Find the maximum element whose frequency is 1
3. Return `-1` if no such element exists

### Complexity analysis

- Time complexity: $O(n)$ — one pass to build the map, one pass over unique elements
- Space complexity: $O(n)$ — the frequency map

```python
from collections import Counter
from typing import List


class Solution:
    def largestUniqueNumber(self, A: List[int]) -> int:
        freq = Counter(A)
        result = -1
        for x, count in freq.items():
            if count == 1:
                result = max(result, x)
        return result
```
