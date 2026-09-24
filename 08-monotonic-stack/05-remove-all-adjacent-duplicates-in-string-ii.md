---
title: Remove All Adjacent Duplicates in String II
difficulty: Medium
leetcode: https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string-ii/
tags:
  - String
  - Stack
---

# Remove All Adjacent Duplicates in String II

## Problem description

Given a string `s` and an integer `k`, repeatedly remove any run of exactly `k` identical consecutive characters. Continue until no such run remains. Return the resulting string.

> **Note on the pattern.** Like [Remove All Adjacent Duplicates In String](./02-remove-all-adjacent-duplicates-in-string.md), this does not use a monotonic stack, but it extends the core skill: the stack now holds richer entries — a character paired with a running count — so a single pass handles arbitrarily long runs and cascading removals.

## Examples

**Example 1:**

```plaintext
Input:  s = "abbbaaca", k = 3
Output: "ca"
Explanation: remove "bbb" → "aaaca", remove "aaa" → "ca"
```

**Example 2:**

```plaintext
Input:  s = "abbaccaa", k = 3
Output: "abbaccaa"
Explanation: no three identical adjacent characters exist
```

**Example 3:**

```plaintext
Input:  s = "abbacccaa", k = 3
Output: "abb"
Explanation: remove "ccc" → "abbaaa", remove "aaa" → "abb"
```

## Constraints

- `1 <= s.length <= 10^5`
- `2 <= k <= 10^4`
- `s` consists of lowercase English letters

## Hints

<details>
<summary>Hint 1</summary>

For `k = 2`, the previous problem's approach works: pop when the top matches. For general `k` you need to track how many times the current character has appeared consecutively at the top of the stack. What data structure entry lets you do that?

</details>

<details>
<summary>Hint 2</summary>

Store `[character, count]` pairs. When the count reaches `k`, the group completes — pop it. The character now at the top might then match what comes next, continuing the cascade without any extra loop.

</details>

## Solution

### Intuition

Each stack entry is a `[char, count]` pair tracking a run of identical characters in the "clean" result so far. When a new character matches the top, increment the count. When the count reaches `k`, pop the entry — the group is complete, just as in the `k = 2` case. Any cascade triggers naturally on the next character.

```plaintext
s = "abbbaaca", k = 3

'a': stack empty, push [a,1].                stack: [[a,1]]
'b': top is 'a' ≠ 'b', push [b,1].          stack: [[a,1],[b,1]]
'b': top is 'b', increment to 2.             stack: [[a,1],[b,2]]
'b': top is 'b', increment to 3 = k → pop.  stack: [[a,1]]
'a': top is 'a', increment to 2.             stack: [[a,2]]
'a': top is 'a', increment to 3 = k → pop.  stack: []
'c': stack empty, push [c,1].                stack: [[c,1]]
'a': top is 'c' ≠ 'a', push [a,1].          stack: [[c,1],[a,1]]

Result: "ca"
```

### Algorithm

1. For each character, if the stack top holds the same character, increment its count; otherwise push `[char, 1]`
2. If the top count reaches `k`, pop it
3. Reconstruct the string by repeating each entry's character by its count

### Complexity analysis

- Time complexity: $O(n)$ — each character is processed once; stack operations are $O(1)$
- Space complexity: $O(n)$ — the stack

```python
class Solution:
    def removeDuplicates(self, s: str, k: int) -> str:
        stack = []  # [char, count]

        for ch in s:
            if stack and stack[-1][0] == ch:
                stack[-1][1] += 1
            else:
                stack.append([ch, 1])

            if stack[-1][1] == k:
                stack.pop()

        return ''.join(ch * count for ch, count in stack)
```
