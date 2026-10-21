---
title: Intervals Intersection
difficulty: Medium
leetcode_title: Interval List Intersections
leetcode: https://leetcode.com/problems/interval-list-intersections/
tags:
  - Array
  - Two Pointers
  - Sweep Line
---

# Intervals Intersection

## Problem description

Given two lists of intervals, find the intersection of these two lists. Each list consists of disjoint intervals sorted on their start time.

## Examples

**Example 1:**

```plaintext
Input: arr1 = [[1, 3], [5, 6], [7, 9]], arr2 = [[2, 3], [5, 7]]
Output: [[2, 3], [5, 6], [7, 7]]
Explanation: The output list contains the common intervals between the two lists.
```

**Example 2:**

```plaintext
Input: arr1 = [[1, 3], [5, 7], [9, 12]], arr2 = [[5, 10]]
Output: [[5, 7], [9, 10]]
Explanation: The output list contains the common intervals between the two lists.
```

## Constraints

- `0 <= len(arr1), len(arr2) <= 1000`
- Each list is sorted by start time and internally disjoint
- `0 <= start <= end <= 10^9`

## Hints

<details>
<summary>Hint 1</summary>

Both lists are already sorted, so you never need to sort or search. Think about walking them together with one index each, the way you would merge two sorted arrays.

</details>

<details>
<summary>Hint 2</summary>

Two intervals overlap on `[max(starts), min(ends)]` — which is empty exactly when that start exceeds that end. After comparing a pair, only the interval that finishes first can be discarded; the other may still intersect what comes next.

</details>

## Solution

### Intuition

Both lists are sorted and internally disjoint, so this is a merge walk rather than a search. Keep one index into each list and repeatedly compare the pair at the front.

For any two intervals, the region they share is bounded by the later start and the earlier end:

```plaintext
intersection = [max(a.start, b.start), min(a.end, b.end)]
```

If that start is greater than that end, the region is empty and the pair simply doesn't overlap. So a single formula handles both the test and the result.

The other half is knowing which index to advance. Because each list is sorted *and* non-overlapping, consecutive intervals in the same list are guaranteed not to overlap — meaning the next interval starts at or after the current one ends.

So when `first_end < second_end`:

```plaintext
second_list[j+1].start  >=  second_list[j].end  >  first_end
```

`first_list[i]` is already over before the next interval in `second_list` even begins. No future pair involving `first_list[i]` can produce an intersection — advance `i`. The symmetric argument applies when `second_end` is smaller. On a tie, both are done and both advance.

```plaintext
arr1 = [[1,3], [5,6], [7,9]],  arr2 = [[2,3], [5,7]]

            1   2   3   4   5   6   7   8   9
arr1        ├───────┤       ├───┤   ├───────┤
arr2            ├───┤       ├───────┤

[1,3] vs [2,3]   ->  [max(1,2), min(3,3)] = [2,3]   ends tie, advance both
[5,6] vs [5,7]   ->  [max(5,5), min(6,7)] = [5,6]   6 < 7, advance arr1
[7,9] vs [5,7]   ->  [max(7,5), min(9,7)] = [7,7]   7 < 9, advance arr2

result: [[2,3], [5,6], [7,7]]
```

### Algorithm

1. Start an index at the front of each list
2. Take the current interval from each and compute `[max(starts), min(ends)]`
3. If that start is at most that end, record it as an intersection
4. Advance the index of whichever interval has the smaller end and if the ends are equal, advance both
5. Stop when either list is exhausted

### Complexity analysis

- Time complexity: $O(n + m)$ — every iteration advances at least one index, and neither index moves backwards.
- Space complexity: $O(1)$ auxiliary — only the two indices are kept. The output list is not counted.

Where `n` and `m` are the lengths of the two lists.

```python
from typing import List


class Solution:
    def interval_intersection(
        self, first_list: List[List[int]], second_list: List[List[int]]
    ) -> List[List[int]]:
        intersections = []

        i = j = 0
        while i < len(first_list) and j < len(second_list):
            first_start, first_end = first_list[i]
            second_start, second_end = second_list[j]

            if first_end >= second_start and second_end >= first_start:
                start = max(first_start, second_start)
                end = min(first_end, second_end)
                intersections.append([start, end])

            # Advance the pointer for whichever interval ends first. Within each list,
            # intervals are sorted and non-overlapping, so the next interval in that same
            # list starts no earlier than the current end — meaning the finished interval
            # can never reach any future interval from the other list.
            if first_end < second_end:
                i += 1
            elif second_end < first_end:
                j += 1
            else:
                i += 1
                j += 1

        return intersections
```
