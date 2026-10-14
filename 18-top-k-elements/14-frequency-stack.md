---
title: Frequency Stack
difficulty: Hard
leetcode_title: Maximum Frequency Stack
leetcode: https://leetcode.com/problems/maximum-frequency-stack/
tags:
  - Hash Table
  - Stack
  - Design
  - Ordered Set
---

# Frequency Stack

## Problem description

Design a class that simulates a stack-like data structure. Implement two operations:

- `push(val)`: push `val` onto the stack.
- `pop()`: remove and return the element with the highest frequency. If there is a tie, remove and return the element closest to the top (most recently pushed).

## Examples

**Example 1:**

```plaintext
Input:
  push(1), push(2), push(3), push(2), push(1), push(2), push(5)
  pop() → 2
  pop() → 1
  pop() → 2

Explanation:
  After all pushes: freq = {1:2, 2:3, 3:1, 5:1}
  pop(): 2 has highest freq (3) → return 2  freq={1:2, 2:2, 3:1, 5:1}
  pop(): 1 and 2 both have freq 2; 1 was pushed more recently among them → return 1
  pop(): 2 has highest freq (2) → return 2
```

## Constraints

- `0 <= val <= 10^9`
- At most `2 * 10^4` calls to `push` and `pop`.
- It is guaranteed that `pop` is not called on an empty stack.

## Hints

<details>
<summary>Hint 1</summary>

A straightforward heap approach recomputes frequencies on every pop. Can you design a structure that answers in $O(1)$ per operation instead?

</details>

<details>
<summary>Hint 2</summary>

Maintain a frequency map `freq[val]` and a group map `group[freq]` that holds a stack of elements pushed at that frequency. Track the current maximum frequency. On `pop`, pull from `group[max_freq]`; if that stack empties, decrement `max_freq`.

</details>
