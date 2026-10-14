---
title: Frequency Sort
difficulty: Medium
leetcode_title: Sort Characters By Frequency
leetcode: https://leetcode.com/problems/sort-characters-by-frequency/
tags:
  - Hash Table
  - String
  - Sorting
  - Heap (Priority Queue)
  - Bucket Sort
  - Counting
---

# Frequency Sort

## Problem description

Given a string, sort its characters in decreasing order based on frequency. If two characters have the same frequency, their relative order in the output does not matter.

## Examples

**Example 1:**

```plaintext
Input: s = "Programming"
Output: "ggmmrrPaoin" (or any valid rearrangement)
Explanation: 'g' and 'm' appear 2 times each, all others appear once.
             The two most frequent come first, the rest follow in any order.
```

**Example 2:**

```plaintext
Input: s = "abcbab"
Output: "bbbaa c" or "bbbcaa" etc.
Explanation: 'b'→3, 'a'→2, 'c'→1. Sorted: bbb + aa + c
```

**Example 3:**

```plaintext
Input: s = "Aabb"
Output: "bbAa" or "bbaa"
Explanation: 'b'→2, 'a'→1, 'A'→1. 'A' and 'a' are different characters.
```

## Constraints

- `1 <= s.length <= 5 * 10^5`
- `s` consists of uppercase and lowercase English letters and digits.

## Hints

<details>
<summary>Hint 1</summary>

You need characters ordered by frequency, highest first. Count frequencies first — then think of each `(char, frequency)` pair as an item to rank.

</details>

<details>
<summary>Hint 2</summary>

After counting, you need to output characters most-frequent first. A max-heap (or sorted list) of `(frequency, char)` pairs gives you this order. Each pop gives you the next most frequent character; repeat it `frequency` times.

</details>

## Solution

### Intuition

Count each character's frequency, then rebuild the string by pulling characters in frequency order, highest first. A max-heap (simulated by negating counts in Python) does this efficiently: each pop gives the most frequent remaining character, which is then repeated `count` times before being appended to the result.

```plaintext
s = "abcbab"

frequencies: {'a':2, 'b':3, 'c':1}

max-heap (by count): [(3,'b'), (2,'a'), (1,'c')]

pop (3,'b') → append 'bbb'   result = "bbb"
pop (2,'a') → append 'aa'    result = "bbbaa"
pop (1,'c') → append 'c'     result = "bbbaac"
```

### Algorithm

1. Count each character's frequency using a hash map.
2. Build a max-heap of `(frequency, character)` pairs.
3. While the heap is non-empty:
   - Pop the most frequent character.
   - Append it `frequency` times to the result.
4. Return the result string.

### Complexity analysis

- Time complexity: $O(n \log n)$ — counting is $O(n)$ + the heap holds at most $n$ distinct characters, so heapify and all pops cost $O(n \log n)$ total.
- Space complexity: $O(n)$ — the frequency map and result string each hold $O(n)$ characters.

```python
import heapq
from collections import Counter


class Solution:
    def frequency_sort(self, s: str) -> str:
        freq = Counter(s)

        # negate count for max-heap behavior
        heap = [(-count, char) for char, count in freq.items()]
        heapq.heapify(heap)

        result = []
        while heap:
            count, char = heapq.heappop(heap)
            result.append(char * (-count))

        return "".join(result)
```
