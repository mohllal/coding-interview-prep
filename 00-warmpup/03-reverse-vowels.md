---
title: Reverse Vowels of a String
difficulty: Easy
leetcode: https://leetcode.com/problems/reverse-vowels-of-a-string/
tags:
  - Two Pointers
  - String
---

# Reverse Vowels of a String

## Problem description

Given a string `s`, reverse only all the vowels in the string and return it.

The vowels are `'a'`, `'e'`, `'i'`, `'o'`, and `'u'`, and they can appear in both lower and upper cases, more than once.

## Examples

**Example 1:**

```plaintext
Input: s = "IceCreAm"
Output: "AceCreIm"
Explanation: The vowels in s are ['I', 'e', 'e', 'A']. On reversing the vowels, s becomes "AceCreIm".
```

**Example 2:**

```plaintext
Input: s = "leetcode"
Output: "leotcede"
```

## Constraints

- `1 <= s.length <= 3 * 10^5`
- `s` consist of **printable ASCII** characters.

## Hints

<details>
<summary>Hint 1</summary>

Only the vowels move; every other character stays exactly where it is. That means you are swapping pairs, not rebuilding the string.

</details>

<details>
<summary>Hint 2</summary>

Walk one pointer in from each end, stopping each only on a vowel. When both have stopped, swap and step past.

</details>

## Solution

The approach uses two pointers starting from both ends of the string. We convert the string to a list for in-place modifications. The left pointer advances until it finds a vowel, and the right pointer retreats until it finds a vowel. When both pointers are on vowels, we swap them and continue until the pointers meet.

### Complexity analysis

- Time complexity: $O(n)$ - Each character is visited at most once by either pointer.
- Space complexity: $O(n)$ - We create a list copy of the string for in-place swapping.

```python
class Solution:
    def reverse_vowels(self, s: str) -> str:
        s_array = list(s)
        vowels = {"a", "e", "i", "o", "u"}

        start = 0
        end = len(s_array) - 1
        while start < end:
            while start < end and s_array[start].lower() not in vowels:
                start += 1

            while start < end and s_array[end].lower() not in vowels:
                end -= 1

            s_array[start], s_array[end] = s_array[end], s_array[start]
            start += 1
            end -= 1

        return "".join(s_array)
```
