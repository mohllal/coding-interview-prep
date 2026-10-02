---
title: Remove K Digits
difficulty: Medium
leetcode: https://leetcode.com/problems/remove-k-digits/
tags:
  - String
  - Stack
  - Greedy
  - Monotonic Stack
---

# Remove K Digits

## Problem description

Given a non-negative integer as a string `num` and an integer `k`, remove exactly `k` digits from `num` to produce the smallest possible integer. Return the result as a string with no leading zeros (except for `"0"` itself).

## Examples

**Example 1:**

```plaintext
Input:  num = "1432219", k = 3
Output: "1219"
Explanation: Remove 4, 3, 2 (the first peak digits) → "1219".
```

**Example 2:**

```plaintext
Input:  num = "10200", k = 1
Output: "200"
Explanation: Remove the leading 1 → "0200" → strip leading zero → "200".
```

**Example 3:**

```plaintext
Input:  num = "1901042", k = 4
Output: "2"
Explanation: Remove 9, 1, 1, 4 → "002" → strip leading zeros → "2".
```

## Constraints

- `1 <= k <= num.length <= 10^5`
- `num` consists of digits only
- `num` has no leading zeros except for `"0"` itself
