---

## title: Merge Intervals

difficulty: Medium
leetcode: <https://leetcode.com/problems/merge-intervals/>
tags:

- Array
- Sorting
- Quicksort

# Merge Intervals

## Problem description

Given a list of intervals, merge all the overlapping intervals to produce a list that has only mutually exclusive intervals.

## Examples

**Example 1:**

```plaintext
Intervals: [[1,4], [2,5], [7,9]]
Output: [[1,5], [7,9]]
Explanation: Since the first two intervals [1,4] and [2,5] overlap, we merged them into one [1,5].
```

**Example 2:**

```plaintext
Intervals: [[6,7], [2,4], [5,9]]
Output: [[2,4], [5,9]]
Explanation: Since the intervals [6,7] and [5,9] overlap, we merged them into one [5,9].
```

**Example 3:**

```plaintext
Intervals: [[1,4], [2,6], [3,5]]
Output: [[1,6]]
Explanation: Since all the given intervals overlap, we merged them into one.
```

## Constraints

- `1 <= len(intervals) <= 10^4`
- `intervals[i].length == 2`
- `0 <= start <= end <= 10^4`

## Hints

Hint 1

In the unsorted input, two intervals that overlap can sit arbitrarily far apart in the list. What single preprocessing step would put every overlapping pair next to each other?

Hint 2

Once sorted by start time, you only ever compare the current interval against the one you are building. If it starts before that one ends they overlap — and the merged end is the later of the two ends, not necessarily the new one.

## Solution

### Intuition

Sorting by start time is what makes this a single pass. Afterwards you know `a.start <= b.start` for every adjacent pair, which collapses the six possible arrangements of two intervals down to four:

```plaintext
1. No overlap  -  a.end < b.start

            1   2   3   4   5   6   7   8   9   10
a           ├───────┤
b                               ├───────────┤

2. Partial overlap  -  b starts inside a and ends after it

            1   2   3   4   5   6   7   8   9   10
a           ├───────────────┤
b                   ├───────────────────┤

3. a fully contains b

            1   2   3   4   5   6   7   8   9   10
a           ├───────────────────────────────┤
b                   ├───────────┤

4. Same start  -  b ends after a

            1   2   3   4   5   6   7   8   9   10
a           ├───────────┤
b           ├───────────────────────────┤
```

Cases 2, 3 and 4 all overlap, and all three merge identically — the result starts where `a` starts and ends at whichever end is later:

```plaintext
case 2      ├───────────────────────────┤       c = [a.start, b.end]
case 3      ├───────────────────────────────┤   c = [a.start, a.end]
case 4      ├───────────────────────────┤       c = [a.start, b.end]
```

So all three reduce to one rule, `c = [a.start, max(a.end, b.end)]`, and the only test you need is whether `b.start <= a.end`. Case 3 is exactly why the merge takes `max` rather than simply `b.end`: an interval nested inside a longer one must not shrink it.

```plaintext
[[1,4], [2,5], [7,9]]  sorted by start

            1   2   3   4   5   6   7   8   9   10
[1,4]       ├───────────┤
[2,5]           ├───────────┤                   2 <= 4, overlap  -> [1, max(4,5)] = [1,5]
[7,9]                               ├───────┤   7 > 5, no overlap -> emit [1,5], start [7,9]

result: [[1,5], [7,9]]
```

### Algorithm

1. Sort the intervals by start time, guaranteeing `a.start <= b.start` for every adjacent pair
2. Hold a running interval, starting with the first one
3. For each following interval, if it starts at or before the running end, extend that end to the `max` of the two ends
4. Otherwise the running interval is final — append it and start a new one
5. Append the last running interval after the loop

### Complexity analysis

- Time complexity: $O(n \log n)$ — dominated by the sort; the scan that follows is a single $O(n)$ pass.
- Space complexity: $O(n)$ — for the output list. Sorting in place adds $O(\log n)$ for the call stack.

```python
from typing import List


class Solution:
    def merge(self, intervals: List[List[int]]) -> List[List[int]]:
        intervals.sort(key=lambda interval: interval[0])

        merged = []
        start, end = intervals[0]

        for current_start, current_end in intervals[1:]:
            if current_start <= end:
                # overlap: extend the running interval, keeping the later end
                end = max(end, current_end)
            else:
                merged.append([start, end])
                start, end = current_start, current_end

        merged.append([start, end])
        return merged
```

## Relationship to [Conflicting Appointments](./04-conflicting-appointments.md)

Asking whether *any* two intervals overlap is the same sort-then-scan, except it stops at the first overlap and returns a boolean instead of merging.
