---
title: Maximum CPU Load
difficulty: Medium
leetcode_title: Car Pooling
leetcode: https://leetcode.com/problems/car-pooling/
tags:
  - Array
  - Sorting
  - Heap (Priority Queue)
  - Simulation
  - Prefix Sum
---

# Maximum CPU Load

## Problem description

We are given a list of jobs. Each job has a start time, an end time, and a CPU load while it is running. Find the maximum CPU load at any point in time, assuming all jobs run on the same machine.

## Examples

**Example 1:**

```plaintext
Jobs: [[1,4,3], [2,5,4], [7,9,6]]
Output: 7
Explanation: Since [1,4,3] and [2,5,4] overlap, their maximum CPU load (3+4=7) will be when both the jobs are running at the same time i.e., during the time interval (2,4).
```

**Example 2:**

```plaintext
Jobs: [[6,7,10], [2,4,11], [8,12,15]]
Output: 15
Explanation: None of the jobs overlap, therefore we will take the maximum load of any job which is 15.
```

**Example 3:**

```plaintext
Jobs: [[1,4,2], [2,4,1], [3,6,5]]
Output: 8
Explanation: Maximum CPU load will be 8 as all jobs overlap during the time interval [3,4].
```

## Constraints

- `1 <= len(jobs) <= 10^4`
- Each job is `[start, end, load]` with `start < end`
- `1 <= load <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

This is [Minimum Meeting Rooms](./05-minimum-meeting-rooms.md) with one substitution. There, every running interval contributed exactly 1 to the count. What does each running job contribute here?

</details>

<details>
<summary>Hint 2</summary>

If you keep a running total instead of a count, you have to subtract the right amount when a job ends — so the structure tracking active jobs has to remember each job's load alongside its end time.

</details>

## Solution

### Intuition

This is [Minimum Meeting Rooms](./05-minimum-meeting-rooms.md) with the counter generalised. There, each active interval contributed `1` and the answer was the heap's size. Here each active job contributes its own `load`, so the answer is a running sum rather than a count.

That one change ripples into the heap's contents: popping a finished job must subtract exactly its load, so the heap stores `(end, load)` pairs rather than bare end times.

```plaintext
[[1,4,2], [2,4,1], [3,6,5]]

            1   2   3   4   5   6
[1,4] load 2 ├───────────┤
[2,4] load 1     ├───────┤
[3,6] load 5         ├───────────┤

t=1: {2}         load 2
t=2: {2,1}       load 3
t=3: {2,1,5}     load 8   <- peak
t=4: jobs ending at 4 retire, {5} remains, load 5
```

Ordering by start time means a job is only ever added after every job that could still be running has been considered, so retiring the finished ones first keeps the running sum exact.

### Algorithm

1. Sort the jobs by start time
2. Keep a min-heap of `(end, load)` for jobs currently running, plus a running `current_load`
3. For each job, pop every entry whose end is at or before this job's start, subtracting its load
4. Add this job's load and push `(end, load)`
5. Track the maximum running load seen

### Complexity analysis

- Time complexity: $O(n \log n)$ — the sort dominates; each job is pushed and popped at most once at $O(\log n)$ each.
- Space complexity: $O(n)$ — the heap holds at most one entry per job.

```python
import heapq
from typing import List


class Solution:
    def find_max_cpu_load(self, jobs: List[List[int]]) -> int:
        jobs.sort(key=lambda job: job[0])

        max_load = 0
        current_load = 0
        running = []  # min-heap of (end, load) for jobs in progress

        for start, end, load in jobs:
            # retire jobs that have finished, removing their contribution
            while running and running[0][0] <= start:
                _, finished_load = heapq.heappop(running)
                current_load -= finished_load

            current_load += load
            heapq.heappush(running, (end, load))
            max_load = max(max_load, current_load)

        return max_load
```

The LeetCode framing, Car Pooling, is the same computation with a threshold on top: it asks whether the peak ever exceeds the vehicle's capacity, which is `find_max_cpu_load(trips) <= capacity`.

## Solution 2: sweep line

### Intuition

Instead of tracking which jobs are active, think about events on a timeline: a job starting adds its load, a job ending removes it. The running total is the CPU load right now, and its peak is the answer.

Emit two events per job — `(start, +load)` and `(end, -load)` — sort them, and sweep.

```plaintext
[[1,4,2], [2,4,1], [3,6,5]]

events sorted: (1,+2)  (2,+1)  (3,+5)  (4,-2)  (4,-1)  (6,-5)

sweep:
  t=1  +2  →  running = 2   peak = 2
  t=2  +1  →  running = 3   peak = 3
  t=3  +5  →  running = 8   peak = 8
  t=4  -2  →  running = 6
  t=4  -1  →  running = 5
  t=6  -5  →  running = 0

answer: 8
```

**Tie-breaking is free here.** In the meeting rooms sweep we needed an explicit encoding (`END = 0, START = 1`) to force ends before starts at the same timestamp. Here we don't — because all loads are positive, every end delta is negative and every start delta is positive. Sorting tuples `(time, delta)` naturally places `(t, -load)` before `(t, +load)` since negative < positive.

### Algorithm

1. For each job emit `(start, +load)` and `(end, -load)`.
2. Sort all events by `(time, delta)` — ends at equal timestamps automatically sort before starts.
3. Sweep, adding each delta to a running total.
4. Return the peak running total.

### Complexity analysis

- Time complexity: $O(n \log n)$ — sorting `2n` events.
- Space complexity: $O(n)$ — the event list.

```python
from typing import List


class Solution:
    def find_max_cpu_load(self, jobs: List[List[int]]) -> int:
        events = []
        for start, end, load in jobs:
            events.append((start, load))
            events.append((end, -load))

        # negative deltas (ends) sort before positive (starts) at equal timestamps
        # because all loads are positive, so no explicit tie-break encoding needed
        events.sort()

        max_load = 0
        running_load = 0
        for _, delta in events:
            running_load += delta
            max_load = max(max_load, running_load)

        return max_load
```
