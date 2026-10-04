---
title: Happy Number
difficulty: Easy
leetcode: https://leetcode.com/problems/happy-number/
tags:
  - Hash Table
  - Math
  - Two Pointers
  - Floyd's Cycle Finding Algorithm
---

# Happy Number

## Problem description

A number is called a happy number if, after repeatedly replacing it with the sum of the squares of its digits, it eventually reaches `1`.

All other (not-happy) numbers will never reach `1`. Instead, they will be stuck in a cycle of numbers which does not include `1`.

## Examples

**Example 1:**

```plaintext
Input: n = 23
Output: true

Steps:
2² + 3² = 4 + 9 = 13
1² + 3² = 1 + 9 = 10
1² + 0² = 1 + 0 = 1 ✓
```

**Example 2:**

```plaintext
Input: n = 12
Output: false

Steps:
1² + 2² = 5
5² = 25
2² + 5² = 29
2² + 9² = 85
8² + 5² = 89
8² + 9² = 145
1² + 4² + 5² = 42
4² + 2² = 20
2² + 0² = 4
4² = 16
1² + 6² = 37
3² + 7² = 58
5² + 8² = 89 ← cycle back to step 5
```

## Constraints

- `1 <= n <= 2³¹ - 1`

## Hints

<details>
<summary>Hint 1</summary>

The sequence either reaches 1 or repeats forever. A set of seen values detects the repeat, but the repeat is a cycle — and cycles have a cheaper test.

</details>

<details>
<summary>Hint 2</summary>

Apply the digit-square step once for the slow pointer and twice for the fast one. If they meet somewhere other than 1, the number is unhappy.

</details>

## Solution 1: Using a hash set

### Intuition

The digit-square-sum process always enters a cycle—either a cycle containing `1` (happy) or a cycle without `1` (unhappy). We can detect when we've seen a number before using a hash set.

### Algorithm

1. Compute sum of squares of digits
2. If result is `1`, return true
3. If result was seen before, return false (cycle detected)
4. Add result to set and repeat

### Complexity analysis

- Time complexity: $O(\log n)$ - only the first iteration, which runs on `n` itself, costs $O(\log n)$ and every iteration after it is a constant amount of work (explained below)
- Space complexity: $O(1)$ - the set only stores values produced by an iteration, which are all at most 810, so it never holds more than a fixed number of small integers, however big `n` is

#### Why the time is $O(\log n)$

A single iteration — summing the squares of the digits — touches each digit once, so its cost is simply the number of digits. A number `n` has about $\log_{10} n$ digits, which is all that $\log n$ means here:

```plaintext
n                 log₁₀ n   digits
9                   0.95       1
999                 3.00       3
1,000,000           6.00       7
2,147,483,647       9.33      10

digits = ⌊log₁₀ n⌋ + 1, so the digit count grows like log n
```

The big input only matters for the **first iteration**, the one that runs on `n` itself. Every digit contributes at most $9^2 = 81$ (which is 10 digits * 81 if all digits are 9), so that one iteration crushes any input down to at most 810. From then on the numbers can never grow back — a 3-digit number maps to at most 3 × 81 = 243 — so every later iteration works on at most 3 digits:

```plaintext
n = 2,147,483,647

iteration 1:   2,147,483,647 → 260      works on 10 digits   ← the only iteration that sees the big n
iteration 2:   260 → 40                 works on 3 digits
iteration 3:   40 → 16                  works on 2 digits
iteration 4:   16 → 37                  works on 2 digits
iteration 5:   37 → 58                  works on 2 digits
...                                     never more than 3 digits again
```

Inside that small range there are at most 999 different values. Since even the largest input (810) is already < 1000, we enter the range [1, 999] immediately (after at most 1 iteration) and once in this range, there are only 999 possible values we can ever see. Therefore, `k` ≤ 999 = $O(1)$, so total time is $O(\log n)$.

```plaintext
total = first step on n's digits  +  at most ~999 steps on tiny numbers
      =         log n             +              constant
      =  O(log n)
```

A repeated value means we have reached the cycle, so the small phase ends within about 999 iterations. That limit is the same whether `n` was 7 or 2 billion, which is exactly what makes it a constant. As `n` grows, only the first iteration gets longer.

```python
class Solution:
    def sum_of_squares(self, n: int) -> int:
        total = 0
        while n > 0:
            total += (n % 10) ** 2
            n = n // 10
        return total

    def is_happy(self, n: int) -> bool:
        visited = set()
        current = self.sum_of_squares(n)

        while current != 1:
            if current in visited:
                return False

            visited.add(current)
            current = self.sum_of_squares(current)

        return True
```

## Solution 2: Floyd's cycle detection

### Intuition

Since the process always leads to a cycle, we can use the fast and slow pointer technique. Instead of storing visited numbers, we detect the cycle by having two "runners" at different speeds. When they meet, we check if the meeting point is `1`.

Think of the sequence as an implicit linked list:

- Each number points to its digit-square-sum
- Unhappy numbers form a cycle not containing `1`
- Happy numbers cycle on `1` (since 1² = 1)

```plaintext
Happy number 23 — the sequence reaches 1, and 1 loops on itself (1² = 1)

23 ──▶ 13 ──▶ 10 ──▶ 1 ──┐
                     ▲   │
                     └───┘

Unhappy number 12 — the sequence falls into a loop of 8 numbers that never contains 1

12 ──▶ 5 ──▶ 25 ──▶ 29 ──▶ 85 ──▶ 89 ──▶ 145 ──▶ 42 ──▶ 20
                                  ▲                     │
                                  │                     ▼
                                  58 ◀── 37 ◀── 16 ◀─── 4
```

Either way the sequence ends in a loop, so slow and fast always meet. The only question is **where**: if they meet on `1`, the number is happy.

```plaintext
n = 23                              n = 12

             slow   fast                         slow   fast
start         23     23             start         12     12
iteration 1   13     10             iteration 1    5     25
iteration 2   10      1             iteration 2   25     85
iteration 3    1      1  ← meet     iteration 3   29    145
                                    iteration 4   85     20
meet on 1 → happy                   iteration 5   89     16
                                    iteration 6  145     58
                                    iteration 7   42    145
                                    iteration 8   20     20  ← meet

                                    meet on 20, not 1 → unhappy
```

### Algorithm

1. Initialize slow at `n`, fast at sum of squares of `n`
2. Move slow one step (one sum), fast two steps (two sums)
3. When they meet, check if meeting point equals `1`

### Complexity analysis

- Time complexity: $O(\log n)$ - Same reasoning as Solution 1, `k` is bounded by a constant
- Space complexity: $O(1)$ - Only two pointers, no extra space used

```python
class Solution:
    def sum_of_squares(self, n: int) -> int:
        total = 0
        while n > 0:
            total += (n % 10) ** 2
            n = n // 10
        return total

    def is_happy(self, n: int) -> bool:
        slow = n
        fast = self.sum_of_squares(n)

        while fast != slow:
            slow = self.sum_of_squares(slow)
            fast = self.sum_of_squares(self.sum_of_squares(fast))

        return fast == 1
```
