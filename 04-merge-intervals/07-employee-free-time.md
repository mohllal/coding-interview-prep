---
title: Employee Free Time
difficulty: Hard
leetcode: https://leetcode.com/problems/employee-free-time/
tags:
  - Array
  - Sweep Line
  - Sorting
  - Heap (Priority Queue)
---

# Employee Free Time

## Problem description

For `k` employees, we are given a list of intervals representing the working hours of each employee. Find the intervals of free time that are common to all employees.

Each employee's list of working hours is sorted by start time.

## Examples

**Example 1:**

```plaintext
Input: Employee Working Hours = [[[1,3], [5,6]], [[2,3], [6,8]]]
Output: [[3,5]]
Explanation: Both the employees are free between [3,5].
```

**Example 2:**

```plaintext
Input: Employee Working Hours = [[[1,3], [9,12]], [[2,4]], [[6,8]]]
Output: [[4,6], [8,9]]
Explanation: All employees are free between [4,6] and [8,9].
```

**Example 3:**

```plaintext
Input: Employee Working Hours = [[[1,3]], [[2,4]], [[3,5], [7,9]]]
Output: [[5,7]]
Explanation: All employees are free between [5,7].
```

## Constraints

- `1 <= len(schedule) <= 50`
- `1 <= len(schedule[i]) <= 50`
- Each employee's intervals are sorted and non-overlapping

## Hints

<details>
<summary>Hint 1</summary>

Free time for *everyone* is time that belongs to no employee at all. If you forget who owns which interval and merge them all into one busy timeline, what do the free intervals become?

</details>

<details>
<summary>Hint 2</summary>

Flattening throws away a guarantee the input gives you: each employee's own list is already sorted. Merging `k` sorted lists is a job for a heap, and it lets you avoid sorting all `n` intervals from scratch.

</details>
