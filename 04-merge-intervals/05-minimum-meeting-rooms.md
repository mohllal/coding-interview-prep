---
title: Minimum Meeting Rooms
difficulty: Medium
leetcode_title: Meeting Rooms II
leetcode: https://leetcode.com/problems/meeting-rooms-ii/
tags:
  - Array
  - Two Pointers
  - Greedy
  - Sorting
  - Heap (Priority Queue)
  - Prefix Sum
---

# Minimum Meeting Rooms

## Problem description

Given a list of intervals representing the start and end time of `n` meetings, find the minimum number of rooms required to hold all the meetings.

## Examples

**Example 1:**

```plaintext
Meetings: [[1,4], [2,5], [7,9]]
Output: 2
Explanation: Since [1,4] and [2,5] overlap, we need two rooms to hold these two meetings. [7,9] can occur in any of the two rooms later.
```

**Example 2:**

```plaintext
Meetings: [[6,7], [2,4], [8,12]]
Output: 1
Explanation: None of the meetings overlap, therefore we only need one room to hold all meetings.
```

**Example 3:**

```plaintext
Meetings: [[1,4], [2,3], [3,6]]
Output: 2
Explanation: Since [1,4] overlaps with the other two meetings [2,3] and [3,6], we need two rooms to hold all the meetings.
```

**Example 4:**

```plaintext
Meetings: [[4,5], [2,3], [2,4], [3,5]]
Output: 2
Explanation: We will need one room for [2,3] and [3,5], and another room for [2,4] and [4,5].
```

## Constraints

- `0 <= len(intervals) <= 10^4`
- `0 <= start < end <= 10^6`

## Hints

<details>
<summary>Hint 1</summary>

"How many intervals overlap somewhere" is not the answer. In `[[2,3], [2,4], [3,5]]` every interval touches another, yet two rooms suffice. What you actually need is the largest number overlapping *at a single instant*.

</details>

<details>
<summary>Hint 2</summary>

Process the meetings in start order and keep the ones currently running. Before starting a new meeting you only care about one of them: the one finishing soonest. Which data structure hands you that in $O(\log n)$?

</details>

## Solution 1: min-heap of end times

### Intuition

Counting overlapping pairs overcounts. If `x` overlaps `y` and `y` overlaps `z`, but `z` starts after `x` ends, then `x` and `z` share a room and only two are needed — not three. The answer is the *peak concurrency*: the largest number of meetings alive at any single instant.

```plaintext
[[2,3], [2,4], [3,5]]

            1   2   3   4   5   6
[2,3]           ├───┤
[2,4]           ├───────┤
[3,5]               ├───────┤

at t=2: 2 alive    at t=3: 2 alive ([2,4] and [3,5]; [2,3] has just ended)
peak = 2, so 2 rooms
```

Walking the meetings in start order, the only thing you need to know before opening a new one is whether any running meeting has already finished — and for that, only the earliest end time matters. A min-heap of end times gives exactly that in $O(\log n)$, and its size is the number of rooms currently occupied.

### Algorithm

1. Sort the meetings by start time
2. Keep a min-heap of the end times of meetings currently in progress
3. For each meeting, pop every end time at or before its start — those rooms are free again
4. Push this meeting's end time
5. The heap size is the number of rooms in use right now; track the maximum

### Complexity analysis

- Time complexity: $O(n \log n)$ — the sort dominates. Each meeting is pushed and popped at most once and each heap operation is $O(\log n)$, so the scan is also $O(n \log n)$.
- Space complexity: $O(n)$ — the heap holds at most one entry per meeting.

```python
import heapq
from typing import List


class Solution:
    def min_meeting_rooms(self, intervals: List[List[int]]) -> int:
        intervals.sort(key=lambda interval: interval[0])

        rooms = 0
        ongoing = []  # min-heap of end times of meetings in progress

        for start, end in intervals:
            # meetings that already finished release their rooms
            while ongoing and ongoing[0] <= start:
                heapq.heappop(ongoing)

            heapq.heappush(ongoing, end)
            rooms = max(rooms, len(ongoing))

        return rooms
```

## Solution 2: sweep line

### Intuition

The question is not "how do I assign meetings to rooms?" It is simpler: **at the busiest moment, how many meetings are happening at once?** That number is the answer.

- You cannot do it with fewer — at the peak, every room is occupied and one meeting would have nowhere to go.
- You can always do it with exactly that many — at every other moment, fewer meetings are running so rooms are available.

Think of it like counting people in a building. Every person entering is `+1`, every person leaving is `-1`. The running total is "how many people are inside right now," and the peak of that total is the minimum size the building needs to be.

Meetings work the same way. `+1` when a meeting starts (a room gets occupied), `-1` when it ends (a room is freed). The running total is "how many rooms are in use right now," and its peak is the answer.

```plaintext
meetings: [[1,4], [2,5], [7,9]]

  t=1  start  →  1 room in use
  t=2  start  →  2 rooms in use  ← peak
  t=4  end    →  1 room in use
  t=5  end    →  0 rooms in use
  t=7  start  →  1 room in use
  t=9  end    →  0 rooms in use

answer: 2
```

We only need to check the count at event boundaries. Between any two consecutive events the count is flat, so the peak can only occur right after a start — checking every timestamp in between would be redundant.

**Tie-breaking at equal timestamps:** when a meeting ends exactly as another begins at `t`, the end must be processed first so the room is freed before the new meeting needs it. Sorting on `(time, kind)` with `END = 0` and `START = 1` ensures this: `(t, 0)` sorts before `(t, 1)` automatically.

### Algorithm

1. Emit two events per meeting: `(start, 1)` and `(end, 0)`
2. Sort by `(time, kind)`, so that at equal times ends are processed before starts
3. Sweep, adding one on a start and subtracting one on an end
4. Track the highest count reached

### Complexity analysis

- Time complexity: $O(n \log n)$ — sorting `2n` events.
- Space complexity: $O(n)$ — the event list.

```python
from typing import List


class Solution:
    def min_meeting_rooms(self, intervals: List[List[int]]) -> int:
        START, END = 1, 0

        events = []
        for start, end in intervals:
            events.append((start, START))
            events.append((end, END))

        # sorting on (time, kind) puts an end before a start at the same timestamp,
        # so a room can be handed straight over
        events.sort()

        rooms = 0
        in_use = 0
        for _, kind in events:
            in_use += 1 if kind == START else -1
            rooms = max(rooms, in_use)

        return rooms
```
