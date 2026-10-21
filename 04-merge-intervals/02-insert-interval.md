---
title: Insert Interval
difficulty: Medium
leetcode: https://leetcode.com/problems/insert-interval/
tags:
  - Array
---

# Insert Interval

## Problem description

Given a list of non-overlapping intervals sorted by their start time, insert a given interval at the correct position and merge all necessary intervals to produce a list that has only mutually exclusive intervals.

## Examples

**Example 1:**

```plaintext
Input: Intervals = [[1,3], [5,7], [8,12]], New Interval = [4,6]
Output: [[1,3], [4,7], [8,12]]
Explanation: After insertion, since [4,6] overlaps with [5,7], we merged them into one [4,7].
```

**Example 2:**

```plaintext
Input: Intervals = [[1,3], [5,7], [8,12]], New Interval = [4,10]
Output: [[1,3], [4,12]]
Explanation: After insertion, since [4,10] overlaps with [5,7] & [8,12], we merged them into [4,12].
```

**Example 3:**

```plaintext
Input: Intervals = [[2,3], [5,7]], New Interval = [1,4]
Output: [[1,4], [5,7]]
Explanation: After insertion, since [1,4] overlaps with [2,3], we merged them into one [1,4].
```

## Constraints

- `0 <= len(intervals) <= 10^4`
- `intervals` is sorted by start time and contains no overlaps
- `0 <= start <= end <= 10^5`

## Hints

<details>
<summary>Hint 1</summary>

The input is already sorted and already free of overlaps. That is a much stronger guarantee than [Merge Intervals](./01-merge-intervals.md) gives you — do you still need to sort?

</details>

<details>
<summary>Hint 2</summary>

Every interval falls into exactly one of three groups: entirely before the new one, overlapping it, or entirely after it. Because the list is sorted, those three groups are contiguous, so you can walk them in order with a single index.

</details>

## Solution

### Intuition

Because the list is already sorted and already disjoint, no sort is needed and the $O(n \log n)$ of [Merge Intervals](./01-merge-intervals.md) drops to $O(n)$.

Sortedness also means the intervals split into three contiguous runs: those that end before the new interval starts, those that overlap it, and those that start after it ends. Walk the three runs in order. Only the middle run does any work — it absorbs each overlapping interval into the new one by widening it on both sides.

```plaintext
intervals = [[1,3], [5,7], [8,12]],  new = [4,10]

            1   2   3   4   5   6   7   8   9   10  11  12
[1,3]       ├───────┤                                         ends before 4  -> copy as-is
new             ├───────────────────────────────┤
[5,7]                       ├───────┤                         overlaps -> new = [4, max(7,10)] = [4,10]
[8,12]                                  ├───────────────┤     overlaps -> new = [4, max(12,10)] = [4,12]

result: [[1,3], [4,12]]
```

Widening takes `min` on the start and `max` on the end, because an overlapping interval may extend past the new one on either side — Example 3 is the case where it extends to the left.

### Algorithm

1. Copy every interval that ends before the new interval starts
2. While the next interval starts at or before the new interval ends, absorb it: set the new start to the `min` of the starts and the new end to the `max` of the ends
3. Append the widened interval
4. Copy the remaining intervals unchanged

### Complexity analysis

- Time complexity: $O(n)$ — each interval is visited by exactly one of the three loops.
- Space complexity: $O(n)$ — for the output list; no extra structures are used.

```python
from typing import List


class Solution:
    def insert(self, intervals: List[List[int]], new_interval: List[int]) -> List[List[int]]:
        merged = []
        i = 0

        # intervals that end before the new one starts
        while i < len(intervals) and intervals[i][1] < new_interval[0]:
            merged.append(intervals[i])
            i += 1

        # intervals that overlap: widen the new interval to cover them
        while i < len(intervals) and intervals[i][0] <= new_interval[1]:
            new_interval[0] = min(new_interval[0], intervals[i][0])
            new_interval[1] = max(new_interval[1], intervals[i][1])
            i += 1

        merged.append(new_interval)

        # intervals that start after the new one ends
        while i < len(intervals):
            merged.append(intervals[i])
            i += 1

        return merged
```
