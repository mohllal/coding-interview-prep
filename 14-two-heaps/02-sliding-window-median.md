---
title: Sliding Window Median
difficulty: Hard
leetcode: https://leetcode.com/problems/sliding-window-median/
tags:
  - Array
  - Hash Table
  - Sliding Window
  - Heap (Priority Queue)
  - Treap
---

# Sliding Window Median

## Problem description

Given an array of numbers and a number `k`, find the median of every `k`-sized subarray (window) of the array.

## Examples

**Example 1:**

```plaintext
Input: nums = [1, 2, -1, 3, 5], k = 2
Output: [1.5, 0.5, 1.0, 4.0]

Explanation: Considering all windows of size 2:
[1, 2] -> median is 1.5
[2, -1] -> median is 0.5
[-1, 3] -> median is 1.0
[3, 5] -> median is 4.0
```

**Example 2:**

```plaintext
Input: nums = [1, 2, -1, 3, 5], k = 3
Output: [1.0, 2.0, 3.0]

Explanation: Considering all windows of size 3:
[1, 2, -1] -> median is 1.0
[2, -1, 3] -> median is 2.0
[-1, 3, 5] -> median is 3.0
```

## Constraints

- `1 <= k <= len(nums) <= 10^5`
- `-2^31 <= nums[i] <= 2^31 - 1`

## Hints

<details>
<summary>Hint 1</summary>

The two-heap split from [Find the Median of a Number Stream](./01-find-the-median-of-a-number-stream.md) still gives you the median in $O(1)$. What that problem never had to do is take a number back *out*.

</details>

<details>
<summary>Hint 2</summary>

A heap cannot delete an arbitrary interior element cheaply — only its root. Rather than hunting for the departing value, what if you left it in place and simply remembered that it is no longer really there?

</details>

## Solution 1: eager removal

### Intuition

The median machinery is unchanged from [Find the Median of a Number Stream](./01-find-the-median-of-a-number-stream.md): a max-heap `low` for the smaller half, a min-heap `high` for the larger half, median read off the roots.

What is new is eviction. Each step adds the arriving number and must also remove the one falling off the left edge — and that value is almost never at a root, so there is no cheap heap operation for it. The direct approach is to scan the heap for it, swap it with the last element, pop, and re-heapify.

```plaintext
nums = [1, 2, -1, 3, 5], k = 3

window [1, 2, -1]      low = [1, -1]   high = [2]     median = 1.0
  add 3, drop 1
window [2, -1, 3]      low = [2, -1]   high = [3]     median = 2.0
  add 5, drop 2
window [-1, 3, 5]      low = [3, -1]   high = [5]     median = 3.0
```

The scan is what makes this $O(k)$ per step. It is correct and easy to follow, but it is the part Solution 2 removes.

### Algorithm

1. Insert the arriving number into `low` or `high` by comparing it against `low`'s root, then rebalance
2. Once the window is `k` wide, read the median off the roots
3. Find the departing number by scanning the heap that holds it, swap it to the end, pop it, and re-heapify
4. Rebalance and continue

### Complexity analysis

- Time complexity: $O(n \cdot k)$ — every one of the `n` steps scans and re-heapifies, which is $O(k)$.
- Space complexity: $O(k)$ — the two heaps together hold exactly the window.

```python
import heapq
from typing import List, Optional


class Solution:
    def _balance(self, low: List[int], high: List[int]) -> None:
        # low may hold one extra element, never more
        if len(low) > len(high) + 1:
            heapq.heappush(high, -heapq.heappop(low))
        elif len(high) > len(low):
            heapq.heappush(low, -heapq.heappop(high))

    def _insert(self, low: List[int], high: List[int], num: int) -> None:
        if not low or -low[0] >= num:
            heapq.heappush(low, -num)
        else:
            heapq.heappush(high, num)

        self._balance(low, high)

    def _remove(self, low: List[int], high: List[int], num: int) -> None:
        # a heap cannot delete an interior element, so find it, swap it out, re-heapify

        # determine which heap contains the number to remove
        if -num in low:
            idx = low.index(-num)
            low[idx] = low[-1]      # swap with last element
            low.pop()               # remove last element
            heapq.heapify(low)      # restore heap order
        elif num in high:
            idx = high.index(num)
            high[idx] = high[-1]    # swap with last element
            high.pop()              # remove last element
            heapq.heapify(high)     # restore heap order
           
        self._balance(low, high)

    def _median(self, low: List[int], high: List[int]) -> Optional[float]:
        if not low and not high:
            return None
        if len(low) == len(high):
            return (-low[0] + high[0]) / 2
        return float(-low[0])

    def median_sliding_window(self, nums: List[int], k: int) -> List[float]:
        low, high = [], []
        medians = []

        window_start = 0
        for window_end in range(len(nums)):
            self._insert(low, high, nums[window_end])

            if window_end >= k - 1:
                medians.append(self._median(low, high))
                self._remove(low, high, nums[window_start])
                window_start += 1

        return medians
```

## Solution 2: lazy removal

### Intuition

The $O(k)$ scan exists only because a heap cannot delete an interior element. So do not delete it. Mark the departing value in a `pending` counter and leave it sitting in the heap as a *ghost*, then discard ghosts only when they surface at a root — where popping is $O(\log k)$.

Because the heaps now hold ghosts, `len(low)` and `len(high)` overcount the window. We therefore maintain explicit `low_size` and `high_size` counters that track only real elements, adjusting them immediately when a value arrives or departs.

The rest of the logic is split into four helpers:

- `push` routes one incoming element to the correct half and rebalances
- `evict` marks one outgoing element as a ghost and rebalances
- `get_median` prunes any ghost roots and reads the answer
- `_rebalance` moves one element between the halves when the sizes drift

**Routing** — which half does an element belong in? Compare `num` against `−low[0]` (the max of `low`), exactly as Solution 1 does. But `low[0]` might be a ghost: a stale value left over from a previous eviction that no longer represents the real boundary. Routing against a ghost can misplace a new element and break the ordering invariant. The fix is to prune `low`'s root before routing, so `−low[0]` is always the real max.

Eviction uses exactly the same routing logic as insertion: mark the ghost first, prune `low`'s root, then compare `num` against the real max of `low`. This works for the same reason — the ordering invariant says every real element of `low` is ≤ real max of `low` and every real element of `high` is > real max of `low`, so the comparison always routes to the correct half.

The ordering matters: prune *before* marking the ghost. If we marked first, `_prune_low()` would immediately see `pending[num] > 0` and pop `num` from low's root — shifting `-low[0]` to the next smaller element. The subsequent routing comparison would then use the wrong boundary and send `num` to the wrong heap. Pruning first freezes `-low[0]` as the real max of low, and we mark and route against that stable boundary. After routing, we prune the attributed heap once more to consume the ghost if it has already surfaced at that root.

### Algorithm

1. Walk the array. On every step, push the incoming element using the previously recorded median as the routing boundary.
2. Once the window is `k` elements wide, record `get_median()` and evict the element falling off the left edge.
3. Repeat until the array is exhausted.

### Complexity analysis

- Time complexity: $O(n \log k)$ — each number is pushed once and popped at most once, at $O(\log k)$ per operation.
- Space complexity: $O(n)$ in the worst case — the heaps hold the window plus however many ghosts have not yet surfaced.

```python
import heapq
from collections import Counter
from typing import List


class Solution:
    def median_sliding_window(self, nums: List[int], k: int) -> List[float]:
        low = []          # max-heap (negated values) — the smaller half
        high = []         # min-heap — the larger half
        low_size = 0      # real element counts, excluding ghosts
        high_size = 0
        pending = Counter()  # evicted values still sitting in a heap as ghosts

        def _prune_low() -> None:
            while low and pending[-low[0]]:
                pending[-low[0]] -= 1
                heapq.heappop(low)

        def _prune_high() -> None:
            while high and pending[high[0]]:
                pending[high[0]] -= 1
                heapq.heappop(high)

        def _rebalance() -> None:
            nonlocal low_size, high_size
            if low_size > high_size + 1:
                _prune_low()                                 # ensure we move a real element
                heapq.heappush(high, -heapq.heappop(low))
                low_size -= 1
                high_size += 1
            elif high_size > low_size:
                _prune_high()                               # ensure we move a real element
                heapq.heappush(low, -heapq.heappop(high))
                high_size -= 1
                low_size += 1

        def push(num: int) -> None:
            nonlocal low_size, high_size
            _prune_low()    # make sure we're comparing against a real boundary
            if not low or num <= -low[0]:
                heapq.heappush(low, -num)
                low_size += 1
            else:
                heapq.heappush(high, num)
                high_size += 1
            _rebalance()

        def evict(num: int) -> None:
            nonlocal low_size, high_size
            _prune_low()       # establish real boundary BEFORE marking ghost
            pending[num] += 1
            if not low or num <= -low[0]:
                low_size -= 1
                _prune_low()   # consume ghost now if it surfaced at low's root
            else:
                high_size -= 1
                _prune_high()  # consume ghost now if it surfaced at high's root
            _rebalance()

        def get_median() -> float:
            _prune_low()
            _prune_high()
            if low_size == high_size:
                return (-low[0] + high[0]) / 2
            return float(-low[0])   # low holds the extra element on odd k

        result = []

        for i, num in enumerate(nums):
            push(num)
            if i >= k - 1:
                median = get_median()
                result.append(median)
                evict(nums[i - k + 1])

        return result
```
