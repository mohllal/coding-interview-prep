---
title: Find the Median of a Number Stream
difficulty: Hard
leetcode_title: Find Median from Data Stream
leetcode: https://leetcode.com/problems/find-median-from-data-stream/
tags:
  - Two Pointers
  - Design
  - Sorting
  - Heap (Priority Queue)
  - Data Stream
---

# Find the Median of a Number Stream

## Problem description

Design a class to calculate the median of a number stream. The class should have two methods:

1. `insert_num(num)` — store the number in the class
2. `find_median()` — return the median of all numbers inserted so far

If the count of numbers inserted is even, the median is the average of the middle two.

## Examples

**Example 1:**

```plaintext
1. insert_num(3)
2. insert_num(1)
3. find_median() -> output: 2
4. insert_num(5)
5. find_median() -> output: 3
6. insert_num(4)
7. find_median() -> output: 3.5
```

## Constraints

- `-10^5 <= num <= 10^5`
- `find_median` is only called after at least one insertion
- Up to `5 * 10^4` calls in total

## Hints

<details>
<summary>Hint 1</summary>

Re-sorting on every query is far too slow, but you do not actually need the whole sequence sorted — only the one or two values sitting in the middle.

</details>

<details>
<summary>Hint 2</summary>

Split the numbers into a smaller half and a larger half. If you could see the biggest of the small half and the smallest of the large half in constant time, the median follows immediately. What two structures give you exactly those two values?

</details>

## Solution

### Intuition

If `x` is the median, then half the numbers are at most `x` and half are at least `x`. So keep the stream physically split into those two halves:

- `low` — the smaller half, in a max-heap, so its root is the largest of the small numbers
- `high` — the larger half, in a min-heap, so its root is the smallest of the large numbers

The median is then always sitting at one or both roots, reachable in $O(1)$.

```plaintext
after inserting 3, 1, 5, 4

    low (max-heap)        high (min-heap)
         3                     4
        /                       \
       1                         5

    roots 3 and 4  ->  even count  ->  median = (3 + 4) / 2 = 3.5
```

Two invariants keep that true. First, every element of `low` must be at most every element of `high`, which is why a new number is routed by comparing it against `low`'s root. Second, the sizes must never differ by more than one, so that the middle really is at the roots — after each insert, if one side has grown too big, move its root across.

The size rule is deliberately asymmetric: `low` is allowed to hold one extra element. That makes the odd case unambiguous, because the median is then simply `low`'s root with no need to ask which heap is larger.

Python only provides a min-heap, so `low` stores negated values and its root is read back as `-low[0]`.

### Algorithm

Insert:

1. If `low` is empty or the number is at most `low`'s root, push it onto `low`; otherwise push it onto `high`
2. If `low` is more than one larger than `high`, move `low`'s root to `high`
3. If `high` is larger than `low`, move `high`'s root to `low`

Find median:

1. If both heaps are the same size, return the average of the two roots
2. Otherwise return `low`'s root, since `low` always holds the extra element

### Complexity analysis

- Time complexity: $O(\log n)$ per insertion, which is one or two heap operations, and $O(1)$ per median query since both roots are read directly. Inserting `n` numbers costs $O(n \log n)$ overall.
- Space complexity: $O(n)$ — every number is stored exactly once across the two heaps.

```python
import heapq


class MedianFinder:
    def __init__(self):
        self.low = []   # max-heap of the smaller half, stored negated
        self.high = []  # min-heap of the larger half

    def insert_num(self, num: int) -> None:
        # route the number so every value in low stays <= every value in high
        if not self.low or -self.low[0] >= num:
            heapq.heappush(self.low, -num)
        else:
            heapq.heappush(self.high, num)

        # rebalance: low may hold one extra element, never more
        if len(self.low) > len(self.high) + 1:
            heapq.heappush(self.high, -heapq.heappop(self.low))
        elif len(self.high) > len(self.low):
            heapq.heappush(self.low, -heapq.heappop(self.high))

    def find_median(self) -> float:
        if self.is_empty():
            return None

        if len(self.low) == len(self.high):
            return (-self.low[0] + self.high[0]) / 2

        # odd count: low always carries the extra element
        return float(-self.low[0])

    def is_empty(self) -> bool:
        return not self.low and not self.high
```
