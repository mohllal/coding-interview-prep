---
title: Remove All Adjacent Duplicates In String
difficulty: Easy
leetcode: https://leetcode.com/problems/remove-all-adjacent-duplicates-in-string/
tags:
  - String
  - Stack
---

# Remove All Adjacent Duplicates In String

## Problem description

Given a string `s` of lowercase letters, repeatedly remove pairs of adjacent identical characters until no more removals are possible. Return the final string.

> **Note on the pattern.** This problem uses a stack but not a monotonic one — nothing enforces an ordering on the stack's contents. It appears here because it builds the core habit every monotonic stack problem depends on: inspect the top, compare against the incoming element, and pop when a condition holds. The ordering invariant arrives in the problems that follow.

## Examples

**Example 1:**

```plaintext
Input:  s = "abccba"
Output: ""
Explanation: "abccba" → remove "cc" → "abba" → remove "bb" → "aa" → remove "aa" → ""
```

**Example 2:**

```plaintext
Input:  s = "foobar"
Output: "fbar"
Explanation: remove "oo" → "fbar"
```

**Example 3:**

```plaintext
Input:  s = "fooobar"
Output: "fobar"
Explanation: remove one "oo" → "fobar" (only one adjacent pair at a time)
```

**Example 4:**

```plaintext
Input:  s = "abcd"
Output: "abcd"
Explanation: no adjacent duplicates
```

## Constraints

- `1 <= s.length <= 10^5`
- `s` consists of lowercase English letters

## Hints

<details>
<summary>Hint 1</summary>

Think about building the result character by character. Before adding a new character, check whether it matches the last character you added. What should happen if it does?

</details>

## Solution

### Intuition

The stack holds the "clean" prefix of the result seen so far. For each incoming character:

- If it matches the top of the stack, they form an adjacent pair — pop the top (both disappear).
- Otherwise, push the character onto the stack.

Because popping can expose a new top that now matches what comes next, collapsing happens naturally across multiple passes without any explicit re-scanning.

```plaintext
s = "abccba"

'a' — stack empty, push.       stack: [a]
'b' — top is 'a' ≠ 'b', push. stack: [a, b]
'c' — top is 'b' ≠ 'c', push. stack: [a, b, c]
'c' — top is 'c' = 'c', pop.  stack: [a, b]
'b' — top is 'b' = 'b', pop.  stack: [a]
'a' — top is 'a' = 'a', pop.  stack: []

Result: ""
```

### Algorithm

1. For each character in `s`, if the stack is non-empty and the top equals the character, pop; otherwise push
2. Join the stack into a string and return it

### Complexity analysis

- Time complexity: $O(n)$ — each character is pushed and popped at most once
- Space complexity: $O(n)$ — the stack

```python
class Solution:
    def removeDuplicates(self, s: str) -> str:
        stack = []
        for ch in s:
            if stack and stack[-1] == ch:
                stack.pop()
            else:
                stack.append(ch)
        return ''.join(stack)
```
