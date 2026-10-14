---
title: Connect Ropes
difficulty: Medium
leetcode_title: Minimum Cost to Connect Sticks
leetcode: https://leetcode.com/problems/minimum-cost-to-connect-sticks/
tags:
  - Array
  - Greedy
  - Heap (Priority Queue)
---

# Connect Ropes

## Problem description

Given `n` ropes with given lengths, connect all the ropes into one rope with minimum total cost. The cost of connecting two ropes is equal to the sum of their lengths.

## Examples

**Example 1:**

```plaintext
Input: ropes = [1, 3, 11, 5]
Output: 33
Explanation:
  Connect 1+3=4   cost: 4    ropes: [4, 11, 5]
  Connect 4+5=9   cost: 9    ropes: [9, 11]
  Connect 9+11=20 cost: 20   ropes: [20]
  Total cost: 4 + 9 + 20 = 33
```

**Example 2:**

```plaintext
Input: ropes = [3, 4, 5, 6]
Output: 36
Explanation:
  Connect 3+4=7   cost: 7    ropes: [5, 6, 7]
  Connect 5+6=11  cost: 11   ropes: [7, 11]
  Connect 7+11=18 cost: 18   ropes: [18]
  Total cost: 7 + 11 + 18 = 36
```

**Example 3:**

```plaintext
Input: ropes = [1, 3, 11, 5, 2]
Output: 42
Explanation:
  Connect 1+2=3   cost: 3    ropes: [3, 3, 5, 11]
  Connect 3+3=6   cost: 6    ropes: [5, 6, 11]
  Connect 5+6=11  cost: 11   ropes: [11, 11]
  Connect 11+11=22 cost: 22  ropes: [22]
  Total cost: 3 + 6 + 11 + 22 = 42
```

## Constraints

- `1 <= sticks.length <= 10^4`
- `1 <= sticks[i] <= 10^4`

## Hints

<details>
<summary>Hint 1</summary>

Each time you connect two ropes, that new rope participates in all future connections. A shorter rope connected early contributes its length to fewer total connections — or does it?

</details>

<details>
<summary>Hint 2</summary>

Every rope's length is added to the total cost once per connection it's involved in. A rope formed at step 1 appears in steps 2, 3, … all the way to the end. To minimize cost, keep combining the two shortest ropes first — this is the same greedy structure as Huffman coding.

</details>

## Solution

### Intuition

Every intermediate rope's length is included in the cost of every subsequent connection it participates in. A rope built early gets paid for multiple times. To minimize the total, the heaviest ropes should be built as late as possible — which means always combining the two shortest ropes first.

This greedy works because each connection reduces the number of ropes by one, and combining the two shortest minimizes the addition to every future connection that rope is part of. It's the same structure as Huffman coding: the optimal merge always picks the two minimum-frequency (here, shortest) elements.

```plaintext
ropes = [1, 3, 11, 5]   (greedy always picks two smallest)

heap: [1, 3, 5, 11]

step 1: pop 1, pop 3   → connect → 4   cost 4    heap: [4, 5, 11]
step 2: pop 4, pop 5   → connect → 9   cost 9    heap: [9, 11]
step 3: pop 9, pop 11  → connect → 20  cost 20   heap: [20]

total: 4 + 9 + 20 = 33  ✓
```

### Algorithm

1. Build a min-heap from all rope lengths.
2. While more than one rope remains:
   - Pop the two shortest ropes.
   - Connect them; add their combined length to the total cost.
   - Push the new rope back onto the heap.
3. Return the total cost.

### Complexity analysis

- Time complexity: $O(n \log n)$ — heapify is $O(n)$; each of the $n - 1$ connections performs two pops and one push, each $O(\log n)$.
- Space complexity: $O(n)$ — the heap holds all rope lengths, modified in place.

```python
import heapq
from typing import List


class Solution:
    def minimum_cost(self, ropes: List[int]) -> int:
        heapq.heapify(ropes)
        total_cost = 0

        while len(ropes) > 1:
            first = heapq.heappop(ropes)
            second = heapq.heappop(ropes)
            cost = first + second
            total_cost += cost
            heapq.heappush(ropes, cost)

        return total_cost
```
