---
title: Balanced Parentheses
difficulty: Easy
leetcode_title: Valid Parentheses
leetcode: https://leetcode.com/problems/valid-parentheses/
tags:
  - String
  - Stack
  - Bracket Sequences
---

# Balanced Parentheses

## Problem description

Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.

## Examples

**Example 1:**

```plaintext
Input: s = "()"
Output: true
```

**Example 2:**

```plaintext
Input: s = "()[]{}"
Output: true
```

**Example 3:**

```plaintext
Input: s = "(]"
Output: false
```

## Constraints

- `1 <= s.length <= 10⁴`
- `s` consists of parentheses only `'()[]{}'`

## Hints

<details>
<summary>Hint 1</summary>

Counting brackets is not enough: `([)]` has equal counts of everything and is still invalid. Order matters.

</details>

<details>
<summary>Hint 2</summary>

The most recently opened bracket must be the first one closed — which is exactly what a stack gives you. Remember to check the stack is empty at the end.

</details>

## Solution

### Intuition

A stack naturally models the nesting structure of brackets. Push opening brackets onto the stack; when encountering a closing bracket, check if the top of the stack has the matching opener.

### Algorithm

1. Create a mapping of closing → opening brackets
2. For each character:
   - If opening: push onto stack
   - If closing: pop and verify it matches
3. Return true if stack is empty at the end

### Complexity analysis

- Time complexity: $O(n)$ — single pass through the string
- Space complexity: $O(n)$ — stack may hold all characters in worst case

```python
class Solution:
    def is_valid(self, s: str) -> bool:
        stack = []
        matching = {')': '(', '}': '{', ']': '['}

        for bracket in s:
            if bracket in matching.values():
                stack.append(bracket)
            else:
                if not stack:
                    return False  # Closing bracket with nothing to match

                if stack.pop() != matching[bracket]:
                    return False  # Mismatched bracket types

        return not stack  # Valid only if all openers were closed
```
