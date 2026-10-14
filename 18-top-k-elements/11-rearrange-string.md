---
title: Rearrange String
difficulty: Medium
leetcode_title: Reorganize String
leetcode: https://leetcode.com/problems/reorganize-string/
tags:
  - Hash Table
  - String
  - Greedy
  - Sorting
  - Heap (Priority Queue)
  - Counting
---

# Rearrange String

## Problem description

Given a string, rearrange it so that no two adjacent characters are the same. If such a rearrangement is not possible, return an empty string.

## Examples

**Example 1:**

```plaintext
Input: s = "aappp"
Output: "papap" or "papap"
Explanation: No two adjacent characters are the same.
```

**Example 2:**

```plaintext
Input: s = "Programming"
Output: "rgmrgmPaoirn" or any valid rearrangement
```

**Example 3:**

```plaintext
Input: s = "aab"
Output: "aba"
```

**Example 4:**

```plaintext
Input: s = "aaab"
Output: ""
Explanation: Impossible — 'a' appears 3 times but the string only has 4 characters.
             No valid rearrangement exists.
```

## Constraints

- `1 <= s.length <= 500`
- `s` consists of lowercase English letters.

## Hints

<details>
<summary>Hint 1</summary>

To avoid placing the same character consecutively, always pick the most frequent remaining character that is different from the previous one placed. What data structure lets you efficiently retrieve the most frequent character at each step?

</details>

<details>
<summary>Hint 2</summary>

After placing a character, its count decreases. You cannot place it again until you've placed at least one different character. One clean way: keep the previously placed character on the side, then push it back after placing the next one.

</details>
