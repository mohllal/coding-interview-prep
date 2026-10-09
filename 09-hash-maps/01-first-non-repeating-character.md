---
title: First Non-Repeating Character
difficulty: Easy
leetcode_title: First Unique Character in a String
leetcode: https://leetcode.com/problems/first-unique-character-in-a-string/
tags:
  - Hash Table
  - String
  - Queue
---

# First Non-Repeating Character

## Problem description

Given a string, identify the position of the first character that appears only once in the string. If no such character exists, return `-1`.

## Examples

**Example 1:**

```plaintext
Input:  "apple"
Output: 0

'a' appears once and is the first such character.
```

**Example 2:**

```plaintext
Input:  "abcab"
Output: 2

'a' and 'b' appear twice. 'c' at index 2 is the first unique character.
```

**Example 3:**

```plaintext
Input:  "abab"
Output: -1

Both 'a' and 'b' appear twice. No unique character exists.
```

## Constraints

- `1 <= s.length <= 10^5`
- `s` consists of only lowercase English letters.

## Hints

<details>
<summary>Hint 1</summary>

You need to know the frequency of each character before you can decide which one is unique. A single pass can build that map.

</details>

<details>
<summary>Hint 2</summary>

Once you have the frequency map, a second pass through the string (in order) lets you find the *first* character with frequency 1 while preserving the original index.

</details>

## Solution

### Intuition

Two passes: first build a frequency map, then scan the string left-to-right and return the index of the first character whose count is 1.

```plaintext
s = "abcab"

Pass 1 — count frequencies:
  a → 2
  b → 2
  c → 1

Pass 2 — scan for first count=1:
  index 0: 'a' → count 2, skip
  index 1: 'b' → count 2, skip
  index 2: 'c' → count 1 ✓ → return 2
```

### Algorithm

1. Build a frequency map of all characters in `s`
2. Iterate through `s` with index
3. Return the index of the first character whose frequency is 1
4. If none found, return `-1`

### Complexity analysis

- Time complexity: $O(n)$ — two linear passes over `s`
- Space complexity: $O(1)$ — the map holds at most 26 lowercase letters

```python
from collections import Counter
from typing import Optional


class Solution:
    def firstUniqChar(self, s: str) -> int:
        freq = Counter(s)

        for i, c in enumerate(s):
            if freq[c] == 1:
                return i

        return -1
```
