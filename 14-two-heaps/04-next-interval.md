---
title: Next Interval
difficulty: Medium
leetcode_title: Find Right Interval
leetcode: https://leetcode.com/problems/find-right-interval/
tags:
  - Array
  - Binary Search
  - Sorting
---

# Next Interval

## Problem description

Given an array of intervals, find the next interval of each interval. For an interval `i`, its next interval `j` is the one with the smallest start that is greater than or equal to the end of `i`.

Return an array containing the index of the next interval of each input interval, or `-1` if there is none. No two intervals share the same start point.

## Examples

**Example 1:**

```plaintext
Input: intervals = [[2, 3], [3, 4], [5, 6]]
Output: [1, 2, -1]
Explanation: The next interval of [2, 3] is [3, 4] (index 1), and the next interval of [3, 4] is [5, 6] (index 2). [5, 6] has no next interval.
```

**Example 2:**

```plaintext
Input: intervals = [[3, 4], [1, 5], [4, 6]]
Output: [2, -1, -1]
Explanation: The next interval of [3, 4] is [4, 6] (index 2). [1, 5] and [4, 6] have no next interval.
```

## Constraints

- `1 <= intervals.length <= 2 * 10^4`
- `intervals[i].length == 2`
- `-10^6 <= start_i <= end_i <= 10^6`
- The start point of each interval is unique.
