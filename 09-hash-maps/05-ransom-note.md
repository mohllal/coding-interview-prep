---
title: Ransom Note
difficulty: Easy
leetcode_title: Ransom Note
leetcode: https://leetcode.com/problems/ransom-note/
tags:
  - Hash Table
  - String
  - Counting
---

# Ransom Note

## Problem description

Given two strings — one representing a ransom note and the other representing the available letters from a magazine — determine if it is possible to construct the ransom note using only the letters from the magazine. Each letter from the magazine can be used only once.

## Examples

**Example 1:**

```plaintext
Input:  ransomNote = "hello", magazine = "hellworld"
Output: true

"hello" can be constructed from the letters in "hellworld".
```

**Example 2:**

```plaintext
Input:  ransomNote = "notes", magazine = "stoned"
Output: true

"notes" can be constructed from "stoned".
```

**Example 3:**

```plaintext
Input:  ransomNote = "apple", magazine = "pale"
Output: false

"apple" needs 2 'p's but "pale" only has 1.
```

## Constraints

- `1 <= ransomNote.length, magazine.length <= 10^5`
- `ransomNote` and `magazine` consist of lowercase English letters.

## Hints

<details>
<summary>Hint 1</summary>

You need to check whether the magazine has at least as many of each letter as the ransom note requires.

</details>

<details>
<summary>Hint 2</summary>

A frequency map of the magazine tells you how many of each letter is available. Then check each letter in the ransom note against that map.

</details>

## Solution

### Intuition

Count the available letters from the magazine, then verify that the ransom note's requirements don't exceed those counts.

```plaintext
ransomNote = "apple"
magazine   = "pale"

Magazine counts:    Ransom note needs:
  p → 1               p → 2  →  1 < 2 ✗ → return False
  a → 1
  l → 1
  e → 1
```

### Algorithm

1. Build a frequency map of `magazine`
2. For each character in `ransomNote`, decrement its count in the map
3. If any count goes below 0, return `False`
4. If all characters are satisfied, return `True`

### Complexity analysis

- Time complexity: $O(n + m)$ — one pass over `magazine`, one pass over `ransomNote`
- Space complexity: $O(1)$ — at most 26 lowercase letters in the map

```python
from collections import Counter


class Solution:
    def canConstruct(self, ransomNote: str, magazine: str) -> bool:
        magazine_freq = Counter(magazine)

        for c in ransomNote:
            magazine_freq[c] -= 1
            if magazine_freq[c] < 0:
                return False

        return True
```
