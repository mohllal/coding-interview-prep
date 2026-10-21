---
title: Conflicting Appointments
difficulty: Easy
leetcode_title: Meeting Rooms
leetcode: https://leetcode.com/problems/meeting-rooms/
tags:
  - Array
  - Sorting
  - Quicksort
---

# Conflicting Appointments

## Problem description

Given an array of intervals representing `n` appointments, find out if a person can attend all of them.

## Examples

**Example 1:**

```plaintext
Appointments: [[1,4], [2,5], [7,9]]
Output: false
Explanation: Since [1,4] and [2,5] overlap, a person cannot attend both of these appointments.
```

**Example 2:**

```plaintext
Appointments: [[6,7], [2,4], [8,12]]
Output: true
Explanation: None of the appointments overlap, therefore a person can attend all of them.
```

**Example 3:**

```plaintext
Appointments: [[4,5], [2,3], [3,6]]
Output: false
Explanation: Since [4,5] and [3,6] overlap, a person cannot attend both of these appointments.
```

## Constraints

- `0 <= len(intervals) <= 10^4`
- `0 <= start < end <= 10^6`

## Hints

<details>
<summary>Hint 1</summary>

You do not need to compare every pair. After one preprocessing step, any conflict that exists is guaranteed to show up between two *adjacent* appointments.

</details>

<details>
<summary>Hint 2</summary>

Decide carefully what counts as a conflict when one appointment ends exactly as the next begins. `[1,4]` and `[4,6]` do not clash — which comparison operator does that imply?

</details>

## Solution

### Intuition

Sort by start time. Then if any conflict exists at all, it must appear between two neighbours: with starts in ascending order, an appointment can only be obstructed by the one immediately before it. That turns an $O(n^2)$ pairwise check into a single scan.

The only subtlety is the boundary. Intervals here are half-open in spirit — an appointment that ends at 4 and one that starts at 4 are fine, because the first is over before the second begins. So the test is strict:

```plaintext
conflict  <=>  current.start < previous.end
```

Using `<=` instead would wrongly reject back-to-back appointments.

```plaintext
[[4,5], [2,3], [3,6]]  sorted -> [[2,3], [3,6], [4,5]]

            1   2   3   4   5   6   7
[2,3]           ├───┤
[3,6]               ├───────────┤          3 < 3 is false -> touching, not a conflict
[4,5]                   ├───┤              4 < 6 is true  -> conflict, return false
```

### Algorithm

1. Sort the appointments by start time
2. Walk adjacent pairs
3. If the current appointment starts strictly before the previous one ends, a conflict exists — return `False`
4. If the scan completes without a conflict, return `True`

### Complexity analysis

- Time complexity: $O(n \log n)$ — the sort dominates; the scan is $O(n)$.
- Space complexity: $O(1)$ auxiliary — sorting in place adds $O(\log n)$ for the call stack.

```python
from typing import List


class Solution:
    def can_attend_meetings(self, intervals: List[List[int]]) -> bool:
        intervals.sort(key=lambda interval: interval[0])

        for i in range(1, len(intervals)):
            previous_end = intervals[i - 1][1]
            current_start = intervals[i][0]

            # touching appointments (a.end == b.start) do not conflict
            if current_start < previous_end:
                return False

        return True
```

## Relationship to [All Conflicting Appointments](./04.1-all-conflicting-appointments.md)

Same sort and same adjacent-pair test. That variation collects every conflicting pair instead of returning on the first one, which changes the return type and the space bound but not the algorithm.
