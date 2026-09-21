---
title: Reverse a String Using Stack
difficulty: Easy
leetcode_title: Reverse String
leetcode: https://leetcode.com/problems/reverse-string/
tags:
  - Two Pointers
  - String
---

# Reverse a String Using Stack

## Problem description

Given a string, write a function that uses a stack to reverse the string. Return the reversed string.

## Examples

**Example 1:**

```plaintext
Input: "Hello, World!"
Output: "!dlroW ,olleH"
```

**Example 2:**

```plaintext
Input: "OpenAI"
Output: "IAnepO"
```

**Example 3:**

```plaintext
Input: "Stacks are fun!"
Output: "!nuf era skcatS"
```

## Constraints

- `1 <= s.length <= 10⁵`
- `s[i]` is a printable ASCII character

## Hints

<details>
<summary>Hint 1</summary>

The last character pushed is the first popped. That property alone does the reversing for you.

</details>

<details>
<summary>Hint 2</summary>

Push every character, then pop them all back into a new string. No index arithmetic is needed.

</details>

## Solution

### Intuition

A stack reverses order naturally: first in, last out. Push all characters onto the stack, then pop them off to build the reversed string.

### Algorithm

1. Push all characters onto the stack
2. Pop each character and append to result
3. Join and return

### Complexity analysis

- Time complexity: $O(n)$ — push and pop each character once
- Space complexity: $O(n)$ — stack holds all characters

```python
class Solution:
    def reverse_string(self, s: str) -> str:
        stack = list(s)  # push all characters
        reversed_list = []

        while stack:
            reversed_list.append(stack.pop())  # LIFO: last char comes out first

        return ''.join(reversed_list)
```
