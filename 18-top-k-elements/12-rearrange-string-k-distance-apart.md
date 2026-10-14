---
title: Rearrange String K Distance Apart
difficulty: Hard
leetcode_title: Rearrange String k Distance Apart
leetcode: https://leetcode.com/problems/rearrange-string-k-distance-apart/
tags:
  - Hash Table
  - String
  - Greedy
  - Sorting
  - Heap (Priority Queue)
  - Counting
---

# Rearrange String K Distance Apart

## Problem description

Given a string `s` and an integer `k`, rearrange `s` such that the same characters are at least `k` positions apart. If it is not possible to rearrange, return an empty string.

## Examples

**Example 1:**

```plaintext
Input: s = "aabbcc", k = 3
Output: "abcabc"
Explanation: Each character is at least 3 apart from its next occurrence.
```

**Example 2:**

```plaintext
Input: s = "aaabc", k = 3
Output: ""
Explanation: Impossible — 'a' must appear 3 times with at least 2 other characters between each pair,
             but there aren't enough non-'a' characters.
```

**Example 3:**

```plaintext
Input: s = "aaadbbcc", k = 2
Output: "abacabcd" or "abacdbc" or similar valid arrangement
```

## Constraints

- `1 <= s.length <= 3 * 10^5`
- `s` consists of lowercase English letters.
- `0 <= k <= s.length`

## Hints

<details>
<summary>Hint 1</summary>

This is a generalization of Rearrange String (k=2). At each step you must pick the most frequent character that was not placed among the last k positions. A max-heap gives the most frequent candidate, but how do you enforce the k-distance constraint?

</details>

<details>
<summary>Hint 2</summary>

Use a queue as a cooldown buffer: after placing a character, push it (with its remaining count) into the queue. Only return it to the heap after k positions have passed. At each step, dequeue from the front and re-add to the heap if the count is still positive.

</details>
