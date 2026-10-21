# Pattern: Merge Intervals

## Overview

The Merge Intervals pattern deals with problems about ranges — meetings, jobs, bookings, any `[start, end]` pair — where the answer depends on how those ranges overlap.

Comparing every pair of intervals is $O(n^2)$. Sorting the intervals first collapses that to a single pass, because once the intervals are in order, any interval that can affect the current one is guaranteed to be adjacent to it.

## Core idea

Sort by start time, then walk the list once, carrying a single piece of state: the interval you are currently building, or the count of ranges currently open.

Sorting is what earns the linear scan. Once `a.start <= b.start` holds for every adjacent pair, the six ways two intervals can sit relative to each other collapse to four, and the only question left is whether `b` begins before `a` has ended:

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

Cases 2, 3 and 4 overlap and merge identically, into `[a.start, max(a.end, b.end)]`. Case 3 is why the end takes a `max` rather than simply `b.end` — a short interval nested inside a long one must not shrink it.

### Why sorting is enough

Without sorting, two overlapping intervals can sit anywhere in the list, so you would have to compare all pairs. After sorting by start time, every interval that could overlap the one you are holding comes immediately next: anything further along starts even later, so if it misses the current interval, it misses everything before it too.

That is what lets the scan discard each interval permanently after one look.

## Templates

```python
function merge(intervals):
    if length(intervals) < 2:
        return intervals

    sort intervals by start time

    result = empty list
    current = intervals[0]

    for each interval from index 1:
        if interval.start <= current.end:
            current.end = max(current.end, interval.end)
        else:
            append current to result
            current = interval

    append current to result
    return result
```

## Variations

### 1. Merge or coalesce

Build a running interval and extend it while the next one overlaps.

- [Merge Intervals](./01-merge-intervals.md)
- [Insert Interval](./02-insert-interval.md).

The state you carry is one interval and emitting it and start a new one whenever the overlap breaks.

### 2. Detect overlap

The same scan, but you only care whether an overlap exists at all, so you return as soon as you find one. This is [Conflicting Appointments](./04-conflicting-appointments.md).

Watch the boundary: intervals that merely touch, where `a.end == b.start`, usually do not count as overlapping. That makes the test strict, `b.start < a.end`.

### 3. Count concurrency with a heap

When the question is "how many at once" rather than "which ranges join up", carry a min-heap of end times instead of a single interval. The heap's root is the next range to expire, and its size is how many are open right now.

This drives which is the same algorithm with each range contributing its own weight rather than 1.

- [Minimum Meeting Rooms](./05-minimum-meeting-rooms.md)
- [Maximum CPU Load](./06-maximum-cpu-load.md), wh

```plaintext
[[1,5], [2,6], [4,8]]

Time: 1   2   3   4   5   6   7   8
      ├───────────┤
          ├───────────┤
                  ├───────────┤

open:  1   2       3   2   1   0
                   ▲
                   peak = 3
```

### 4. Sweep the endpoints

Discard the intervals entirely and keep only their endpoints as events: `+1` at a start, `-1` at an end. Sort the events and sweep, tracking a running count.

Ties decide correctness here. When one range ends exactly as another begins, the end must be processed first, or a handover is miscounted as an overlap. Sort on `(time, kind)` with ends ordered before starts.

### 5. Two sorted lists at once

When you are given two already-sorted lists, walk them together with an index each rather than merging and re-sorting.

- [Intervals Intersection](./03-intervals-intersection.md).

Two intervals share `[max(starts), min(ends)]`, which is empty exactly when that start exceeds that end. Advance whichever interval ends first — it cannot reach anything later in the other list.

## Recognize it when

- The input is a list of `[start, end]` pairs, or anything shaped like one: meetings, jobs, bookings, flights
- The question asks about overlapping, merging, inserting, intersecting, or gaps
- The question asks how many things happen *at the same time*, or the peak of some load
- You find yourself about to compare every interval with every other one

Phrases like *overlapping*, *conflicting*, *busy*, *free time*, *at the same time* and *minimum rooms* are the strong signals.

## Reach for something else when

- Ranges are given as a start plus a duration with no fixed timeline, and nothing is ever compared: usually a scheduling or greedy problem, not an interval one.
- The values are indices into an array and you need contiguous elements rather than overlap: [Sliding Window](../03-sliding-window/README.md).
- The timeline is small and dense and you need many point queries: a difference array or Prefix Sum over the timeline beats sorting.
- You must support intervals being inserted and deleted while answering queries: an interval tree or an ordered map, not a one-pass scan.

## Pitfalls

- Forgetting to sort. Nearly every bug in this pattern traces back to assuming order that is not there.
- Taking `b.end` instead of `max(a.end, b.end)` when merging, which silently truncates a nested interval.
- Getting the touching case wrong. Decide whether `[1,4]` and `[4,6]` overlap, then pick `<` or `<=` deliberately.
- In a sweep, leaving ties to chance. Process ends before starts at equal timestamps.
- Mutating the caller's intervals while merging. Build new pairs rather than writing into the input.

## Key takeaways

- Sort by start time first and it turns an $O(n^2)$ pairwise comparison into one $O(n \log n)$ pass.
- After sorting, overlap is the single test `b.start <= a.end`, and merging is always `[a.start, max(a.end, b.end)]`.
- Carry one interval when merging, a min-heap of end times when counting concurrency.
- "How many at once" is a sweep or a heap, never a pairwise count of overlaps.
- When two lists are already sorted, walk them together instead of re-sorting.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
