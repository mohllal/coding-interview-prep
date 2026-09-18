---
title: Sqrt(x)
difficulty: Easy
leetcode: https://leetcode.com/problems/sqrtx/
tags:
  - Math
  - Binary Search
  - Newton's Method
---

# Sqrt(x)

## Problem description

Given a non-negative integer `x`, return the square root of `x` rounded down to the nearest integer. The returned integer should be **non-negative** as well.

You **must not use** any built-in exponent function or operator.

- For example, do not use `pow(x, 0.5)` in c++ or `x ** 0.5` in python.

## Examples

**Example 1:**

```plaintext
Input: x = 4
Output: 2
Explanation: The square root of 4 is 2, so we return 2.
```

**Example 2:**

```plaintext
Input: x = 8
Output: 2
Explanation: The square root of 8 is 2.82842..., and since we round it down to the nearest integer, 2 is returned.
```

## Constraints

- `0 <= x <= 2^31 - 1`

## Hints

<details>
<summary>Hint 1</summary>

The answer is somewhere in `[0, x]`, and the relation "is `m * m` too big?" is monotonic — once a candidate is too large, everything above it is too.

</details>

<details>
<summary>Hint 2</summary>

Binary search on the answer. Be careful about which bound you keep when `m * m` overshoots, since you want the floor of the root, not the nearest value.

</details>

## Solution 1: Using a linear search

Walk upwards from `1`, squaring each candidate, and stop as soon as the square passes `x`. The last candidate that did not overshoot is the answer.

No explicit upper limit is needed here: the loop condition `i * i <= x` bounds the search on its own, exiting the moment `i` reaches $\sqrt{x}$.

It is tempting to cap this at `x // 2` the way the binary search below does, but that bound is the *looser* of the two, not the tighter one — at the constraint ceiling it is `1,073,741,823` against `46,340`, roughly 23,000 times wider.

A linear scan pays for a loose bound in direct proportion, so swapping the condition for `x // 2` would turn an $O(\sqrt{x})$ loop into an $O(x)$ one. Binary search pays only the *logarithm* of its bound, which is why the same looseness costs it 30 iterations instead of 15 and nothing more. See [why the upper bound is `x // 2`](#why-the-upper-bound-is-x--2).

### Complexity analysis

- Time complexity: $O(\sqrt{x})$ – the loop runs once per integer from `1` up to $\sqrt{x}$.
- Space complexity: $O(1)$ – a single counter.

```python
class Solution:
    def my_sqrt(self, x: int) -> int:
        if x < 2:
            return x

        i = 1
        while i * i <= x:
            i += 1
        return i - 1
```

## Solution 2: Binary search

Binary search finds the largest integer whose square does not exceed `x`. The answer must lie in `[1, x // 2]`, so each step compares `mid * mid` against `x` and discards half the remaining range. When the loop ends, `right` holds the floor of the square root.

### Why the upper bound is x // 2

Unlike the linear scan, binary search needs its range stated up front — it cannot discover the end by walking into it. So the bound has to be something computable without already knowing the answer, which rules out $\sqrt{x}$ itself.

`x // 2` qualifies, and the claim `sqrt(x) <= x / 2` rearranges into something obvious:

```plaintext
sqrt(x) <= x / 2            multiply both sides by 2
2 * sqrt(x) <= x            square both sides (both are non-negative)
4x <= x²                    divide by x (positive)
4 <= x
```

So the bound is valid for every `x >= 4`, and `x = 4` is the one case where it is exact rather than loose — `sqrt(4)` is `2`, which is precisely `4 // 2`.

The two values below that still work out, because rounding down closes the gap:

```plaintext
x      x // 2    floor(sqrt(x))     holds?
0        0            0             early return
1        0            1             early return
2        1            1             1 <= 1   ✓
3        1            1             1 <= 1   ✓
4        2            2             2 <= 2   ✓  exact
9        4            3             3 <= 4   ✓
100     50           10            10 <= 50  ✓
```

The `x < 2` guard is doing real work in that table. At `x = 1` the range would be `[1, 0]`, which is empty, so the loop never runs and `right` is returned as `0` — wrong. Returning `x` directly for `0` and `1` sidesteps it.

### Complexity analysis

- Time complexity: $O(\log x)$ – each iteration halves the search space.
- Space complexity: $O(1)$ – two pointers.

```python
class Solution:
    def my_sqrt(self, x: int) -> int:
        if x < 2:
            return x

        left = 1
        right = x // 2
        while left <= right:
            mid = (left + right) // 2
            number = mid * mid
            if number < x:
                left = mid + 1
            elif number > x:
                right = mid - 1
            else:
                return mid # perfect square e.g. sqrt(9) = 3 * 3

        return right
```
