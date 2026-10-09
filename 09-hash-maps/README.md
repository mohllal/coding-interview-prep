# Pattern: Hash Maps

## Overview

A hash map (also called a hash table or dictionary) is a data structure that maps keys to values in average $O(1)$ time for insertion, lookup, and deletion. In interview problems, it is the go-to tool when you need to count things, check membership, or remember where you've seen something before.

## Core idea

The fundamental loop is always the same: iterate over the input once, consult the map to answer a question about what you've seen so far, then update the map.

```plaintext
pass 1 — build a frequency map:

  "balloon"
   b → 1
   a → 1
   l → 2
   o → 2
   n → 1

pass 2 — answer the question using counts:

  "balloon" needs: b×1, a×1, l×2, o×2, n×1
  floor-divide each required count into what's available
  take the minimum → that's how many "balloon"s you can form
```

## Variations

### 1. Frequency counting

Count occurrences of each element with `Counter` or a plain dict. Used to find the first unique element, the most common, or anything requiring knowing how many times each element appeared.

### 2. Membership / "have I seen this?"

Store elements as keys (values can be `True`, an index, or a complement). Classic use: two-sum (`target - x` lookup), detecting duplicates, or first non-repeating character.

### 3. Constructing from counts

Build a frequency map of what you have and what you need, then compute how much of the target you can satisfy using floor division per required character. Maximum Number of Balloons and Ransom Note both fit here.

## Templates

**Frequency map:**

```python
from collections import Counter

freq = Counter(s)          # or: freq = {}; for c in s: freq[c] = freq.get(c, 0) + 1
```

**First unique element:**

```python
freq = Counter(s)
for i, c in enumerate(s):
    if freq[c] == 1:
        return i
return -1
```

## Recognize it when

- The problem asks to count or compare occurrences of elements.
- You need to answer "have I seen X before?" in $O(1)$ — a set or map replaces a linear scan.
- You are checking whether one string or list can be built from another (membership or frequency check).
- An $O(n^2)$ brute-force solution is scanning for something on every element — a map lets you pre-index that scan.
- The problem asks for the first, last, or most frequent occurrence of something.

## Reach for something else when

- Order matters and you need the *k*-th largest — use a heap.
- You need range queries over indices — use prefix sums.
- The problem is about a contiguous subarray satisfying some condition — consider sliding window.
- The array is sorted and you're looking for a pair — converging two pointers fits better.

## Pitfalls

- `Counter` returns `0` for missing keys, but a plain `dict` raises `KeyError` on missing access — use `.get(key, 0)` with plain dicts.
- Double-counting: some characters need to appear multiple times per "use" (e.g. 'l' and 'o' in "balloon") — use `//` not just presence.
- Off-by-one in palindrome construction: remember that at most one odd-count character goes in the center, not one per character with an odd count.

## Key takeaways

- A frequency map built in one pass turns an $O(n^2)$ scan into an $O(n)$ lookup in the second pass.
- `Counter` from `collections` handles all frequency-map boilerplate; its missing-key default of `0` avoids `KeyError` guards.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
