---
title: Decimal to Binary Conversion
difficulty: Easy
leetcode_title: Convert a Number to Hexadecimal
leetcode: https://leetcode.com/problems/convert-a-number-to-hexadecimal/
tags:
  - Math
  - String
  - Bit Manipulation
---

# Decimal to Binary Conversion

## Problem description

Given a positive integer `n`, write a function that returns its binary equivalent as a string. Do not use any built-in binary conversion function.

## Examples

**Example 1:**

```plaintext
Input: n = 2
Output: "10"
Explanation: The binary equivalent of 2 is 10.
```

**Example 2:**

```plaintext
Input: n = 7
Output: "111"
Explanation: The binary equivalent of 7 is 111.
```

**Example 3:**

```plaintext
Input: n = 18
Output: "10010"
Explanation: The binary equivalent of 18 is 10010.
```

## Constraints

- `1 <= n <= 10⁹`

## Hints

<details>
<summary>Hint 1</summary>

Repeated division by 2 produces the binary digits — but check which end they come out of.

</details>

<details>
<summary>Hint 2</summary>

They come out least-significant first, which is the reverse of how you want to print them. That is a stack.

</details>

## Solution

### Intuition

To convert decimal to binary, repeatedly divide by 2 and collect remainders. The remainders come out in reverse order (LSB first), so a stack naturally reverses them to produce the correct binary string.

### Algorithm

1. While `n != 0`:
   - Push `n % 2` onto the stack
   - Set `n = n // 2`
2. Pop all elements and join to form the binary string

### Complexity analysis

- Time complexity: $O(\log n)$ — number of bits in n
- Space complexity: $O(\log n)$ — stack holds all bits

```python
class Solution: 
    def decimal_to_binary(self, num: int) -> str:
        if num == 0:
            return "0"

        bits = []

        while num != 0:
            bits.append(str(num % 2))  # Remainder is the bit (LSB first)
            num //= 2
        
        return "".join(reversed(bits))  # Reverse to get MSB first
```
