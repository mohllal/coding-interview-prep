---
title: Longest Palindrome
difficulty: Easy
leetcode_title: Longest Palindrome
leetcode: https://leetcode.com/problems/longest-palindrome/
tags:
  - Hash Table
  - String
  - Greedy
---

# Longest Palindrome

## Problem description

Given a string, determine the length of the longest palindrome that can be constructed using the characters from the string. You don't need to return the palindrome itself, just its maximum possible length.

## Examples

**Example 1:**

```plaintext
Input:  "applepie"
Output: 5

"pepep" (or "epiee", etc.) — longest palindrome has length 5.
```

**Example 2:**

```plaintext
Input:  "aabbcc"
Output: 6

"abccba" uses all characters, length 6.
```

**Example 3:**

```plaintext
Input:  "bananas"
Output: 5

"anana" — length 5.
```

## Constraints

- `1 <= s.length <= 2000`
- `s` consists of lowercase and/or uppercase English letters only.

## Hints

<details>
<summary>Hint 1</summary>

A palindrome reads the same forwards and backwards. What does that imply about how many times each character appears in it?

</details>

<details>
<summary>Hint 2</summary>

Every character with an even count can be fully used. For characters with odd counts, only an even number of them can be placed symmetrically — with at most one odd-count character placed in the center.

</details>

## Solution

### Intuition

A palindrome mirrors around its center. Every character that contributes to both halves must appear an even number of times. Any character with an odd count can contribute its largest even portion (`count - 1`), and exactly one character with an odd count can occupy the center position.

```plaintext
s = "bananas"

Counts:
  b → 1
  a → 3
  n → 2
  s → 1

For each count:
  b → odd  → add count - 1 = 0,  found_odd = True
  a → odd  → add count - 1 = 2,  found_odd = True
  n → even → add count     = 2
  s → odd  → add count - 1 = 0,  found_odd = True

length so far = 0 + 2 + 2 + 0 = 4
found_odd → add 1 for center

Length = 5
```

### Algorithm

1. Count the frequency of each character
2. For each count: if even, add it fully; if odd, add `count - 1` and mark `found_odd`
3. If `found_odd`, add 1 for the center

### Complexity analysis

- Time complexity: $O(n)$ — one pass to count, one pass over the frequency map
- Space complexity: $O(1)$ — at most 52 distinct characters (upper + lower case)

```python
from collections import Counter


class Solution:
    def longestPalindrome(self, s: str) -> int:
        freq = Counter(s)

        length = 0
        found_odd = False

        for count in freq.values():
            if count % 2 == 0:
                length += count
            else:
                length += count - 1
                found_odd = True

        if found_odd:
            length += 1

        return length
```
