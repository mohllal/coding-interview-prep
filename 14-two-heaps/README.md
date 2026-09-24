# Pattern: Two Heaps

## Overview

The Two Heaps pattern keeps a collection split into two halves, each held in its own heap, so that the boundary between them is always visible in $O(1)$.

It applies when you need a value from the *middle* of a dataset — most often the median — while elements keep arriving. Re-sorting on every query would be $O(n \log n)$ each time, but you rarely need the whole order: only the one or two values at the dividing line.

## Core idea

Split the data into a smaller half and a larger half, and store each so that its inner edge is at a root:

- the smaller half in a max-heap, whose root is the largest of the small values
- the larger half in a min-heap, whose root is the smallest of the large values

Those two roots are the elements straddling the middle, so the answer is always one of them or their average.

```plaintext
values 1, 3, 4, 5

    low (max-heap)        high (min-heap)
         3                     4
        /                       \
       1                         5

    everything in low  <=  everything in high
    roots 3 and 4 straddle the middle  ->  median = (3 + 4) / 2 = 3.5
```

Two invariants make it work, and every problem in this pattern is about maintaining them:

1. Ordering — every element in `low` is at most every element in `high`. A new value is routed by comparing it against `low`'s root.
2. Balance — the two sizes never differ by more than one. After each insert, if one side has grown too big, move its root across.

The balance rule is usually made asymmetric on purpose: let `low` hold the extra element when the count is odd. Then the odd case needs no branching — the median is just `low`'s root.

For a heap of `n` elements, insert and remove cost $O(\log n)$ and reading the root costs $O(1)$. Python's `heapq` is a min-heap only, so a max-heap is simulated by negating values on the way in and out.

## Variations

### 1. Static stream

Numbers only ever arrive. Insert, rebalance, read the median off the roots. This is [Find the Median of a Number Stream](./01-find-the-median-of-a-number-stream.md).

### 2. Sliding window

Numbers arrive *and* depart, which is the hard part — a heap can only remove its root cheaply, and the departing value is almost never there.

Two ways out, both in [Sliding Window Median](./02-sliding-window-median.md):

- Eager removal: find the value, swap it to the end, pop, re-heapify. Simple, but $O(k)$ per step.
- Lazy removal: leave it in place, record it in a `to_remove` tally, and discard it only when it surfaces at a root. $O(\log k)$ amortised, at the cost of the heaps holding ghosts — so sizes must be tracked in a separate counter rather than read from `len()`.

### 3. Two heaps for scheduling

The same two-structure idea appears without a median: a min-heap of end times plus a running total is how [Minimum Meeting Rooms](../04-merge-intervals/05-minimum-meeting-rooms.md) and [Maximum CPU Load](../04-merge-intervals/06-maximum-cpu-load.md) track what is currently active.

## Template

Two operations, both $O(\log n)$. Always push to `low` first, then run two independent fixes:

```python
import heapq

low = []   # max-heap (negate values) — the smaller half
high = []  # min-heap — the larger half


def insert(num):
    # route to the correct half first
    if not low or num <= -low[0]:
        heapq.heappush(low, -num)
    else:
        heapq.heappush(high, num)

    # fix balance: low carries the extra element on odd counts
    if len(low) > len(high) + 1:
        heapq.heappush(high, -heapq.heappop(low))
    elif len(high) > len(low):
        heapq.heappush(low, -heapq.heappop(high))


def find_median():
    if len(low) == len(high):
        return (-low[0] + high[0]) / 2
    return -low[0]   # low carries the extra
```

Why always push to `low` first: the ordering check then has a non-empty `low` to compare against, so it can move the number to `high` if it belongs there. The balance check that follows is independent — it does not know or care why one side is larger, only that it is.

## Recognize it when

- You need the median, and the data keeps changing
- You need the `k`-th smallest or largest while elements arrive or depart
- The problem splits naturally into a "smaller half" and a "larger half"
- You want the largest of one group and the smallest of another, both cheaply
- Re-sorting on every query would be correct but too slow

## Reach for something else when

- The data is static and you need one median: a quickselect gives $O(n)$ on average with no structure to maintain.
- You need the `k` largest overall rather than something in the middle: a single heap of size `k` is enough.
- You need arbitrary rank queries or ordered iteration: an order-statistic tree or balanced BST does what two heaps cannot.
- You need the maximum or minimum of a sliding window rather than its median: a monotonic deque is $O(1)$ amortised and far simpler.

## Pitfalls

- Forgetting to negate consistently when simulating a max-heap with `heapq`. Negate on push and again on read.
- Rebalancing before routing. Insert first, then fix the sizes.
- Letting the size rule be ambiguous. Pick a side to carry the extra element and stick to it, or the odd case needs a branch.
- With lazy deletion, trusting `len(heap)` for the real size. Ghosts inflate it; keep a separate counter.
- Returning an integer where a float is expected. The median of an even count is an average.

## Key takeaways

- Two heaps expose the middle of a dataset in $O(1)$, with $O(\log n)$ updates.
- The max-heap holds the smaller half, the min-heap the larger; the median lives at the roots.
- Maintain two invariants after every change: ordering across the halves, and sizes differing by at most one.
- Let one side carry the extra element so the odd case has no branch.
- Deletion is the hard part. When elements leave, prefer lazy removal with a pending-removal tally.

## Python heapq helper

`heapq` is a min-heap only. Simulate a max-heap by negating values on push and again on read:

```python
import heapq

# Min-heap
min_heap = []
heapq.heappush(min_heap, val)
smallest = heapq.heappop(min_heap)
peek = min_heap[0]

# Max-heap — negate on push, negate on pop/peek
max_heap = []
heapq.heappush(max_heap, -val)
largest = -heapq.heappop(max_heap)
peek = -max_heap[0]
```

See [00-heap-implementation.md](./00-heap-implementation.md) for bubble-up / bubble-down from scratch.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
