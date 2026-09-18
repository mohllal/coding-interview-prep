---
title: Valid Anagram
difficulty: Easy
leetcode: https://leetcode.com/problems/valid-anagram/
tags:
  - Hash Table
  - String
  - Sorting
---

# Valid Anagram

## Problem description

Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.

## Examples

**Example 1:**

```plaintext
Input: s = "anagram", t = "nagaram"
Output: true
```

**Example 2:**

```plaintext
Input: s = "rat", t = "car"
Output: false
```

## Constraints

- `1 <= s.length, t.length <= 5 * 10^4`
- `s` and `t` consist of lowercase English letters.

**Follow up:** What if the inputs contain Unicode characters? How would you adapt your solution to such a case?

## Hints

<details>
<summary>Hint 1</summary>

Two strings are anagrams exactly when they have the same multiset of characters. What does that say about their lengths?

</details>

<details>
<summary>Hint 2</summary>

Count the characters of one string, then spend those counts down while scanning the other. Any count going negative, or anything left over, means no.

</details>

## Solution

The approach first checks if the strings have equal length (a necessary condition for anagrams). Then it uses a hash map to count character frequencies in both strings. Finally, it verifies that every character appears the same number of times in both strings by comparing the counts bidirectionally.

### Complexity analysis

- Time complexity: $O(n)$ - Linear pass to count characters and compare counts.
- Space complexity: $O(1)$ - The counter is bounded by 26 lowercase letters since input strings only contain english alphabet characters.

```python
from collections import Counter

class Solution:
    def is_anagram(self, s: str, t: str) -> bool:
        if len(s) != len(t):
            return False

        s_counter = Counter(s)
        t_counter = Counter(t)

        for letter, count in t_counter.items():
            if s_counter[letter] != count:
                return False
        
        for letter, count in s_counter.items():
            if t_counter[letter] != count:
                return False

        return True
```
