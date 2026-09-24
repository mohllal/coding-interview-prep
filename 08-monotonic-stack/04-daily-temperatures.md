---
title: Daily Temperatures
difficulty: Medium
leetcode: https://leetcode.com/problems/daily-temperatures/
tags:
  - Array
  - Stack
  - Monotonic Stack
---

# Daily Temperatures

## Problem description

Given an array of integers `temperatures` representing daily temperatures, return an array `answer` where `answer[i]` is the number of days you have to wait after day `i` to get a warmer temperature. If no future day is warmer, put `0`.

## Examples

**Example 1:**

```plaintext
Input:  temperatures = [70, 73, 75, 71, 69, 72, 76, 73]
Output: [1, 1, 4, 2, 1, 1, 0, 0]
Explanation: Day 0 (70°) waits 1 day for 73°. Day 2 (75°) waits 4 days for 76°. Etc.
```

**Example 2:**

```plaintext
Input:  temperatures = [73, 72, 71, 70]
Output: [0, 0, 0, 0]
Explanation: Temperatures only decrease — no warmer day follows.
```

**Example 3:**

```plaintext
Input:  temperatures = [70, 71, 72, 73]
Output: [1, 1, 1, 0]
Explanation: Each day is followed immediately by a warmer one, except the last.
```

## Constraints

- `1 <= temperatures.length <= 10^5`
- `30 <= temperatures[i] <= 100`

## Hints

<details>
<summary>Hint 1</summary>

This is the same "next greater element" pattern as [Next Greater Element](./03-Next%20Greater%20Element.md), but you need the *distance* to the next warmer day, not the temperature itself. What should you store on the stack — values or indices?

</details>

## Solution

### Intuition

The stack holds the **indices** of days still waiting for a warmer future day, in a monotonically non-increasing order of their temperatures. When a new day arrives with a higher temperature, every waiting day that is colder than today just got its answer: `today - waiting_day`.

```plaintext
temperatures = [70, 73, 75, 71, 69, 72, 76, 73]

i=0 (70°): push 0.             stack: [0]
i=1 (73°): 73>70 → ans[0]=1-0=1, pop. push 1.   stack: [1]
i=2 (75°): 75>73 → ans[1]=2-1=1, pop. push 2.   stack: [2]
i=3 (71°): 71<75, push 3.      stack: [2, 3]
i=4 (69°): 69<71, push 4.      stack: [2, 3, 4]
i=5 (72°): 72>69 → ans[4]=5-4=1, pop.
           72>71 → ans[3]=5-3=2, pop.
           72<75, push 5.       stack: [2, 5]
i=6 (76°): 76>72 → ans[5]=6-5=1, pop.
           76>75 → ans[2]=6-2=4, pop.
           push 6.              stack: [6]
i=7 (73°): 73<76, push 7.      stack: [6, 7]

Remaining [6, 7] → ans[6]=ans[7]=0 (already initialised to 0)

Output: [1, 1, 4, 2, 1, 1, 0, 0]
```

### Algorithm

1. Initialise `result` to all zeros
2. Maintain a stack of indices; temperatures at those indices are non-increasing
3. For each day `i`, pop every index `j` where `temperatures[j] < temperatures[i]` and set `result[j] = i - j`
4. Push `i`; anything remaining in the stack at the end keeps its `0`

### Complexity analysis

- Time complexity: $O(n)$ — each index is pushed and popped at most once
- Space complexity: $O(n)$ — the stack

```python
from typing import List


class Solution:
    def dailyTemperatures(self, temperatures: List[int]) -> List[int]:
        result = [0] * len(temperatures)
        stack = []  # indices; temperatures[stack[i]] are non-increasing

        for i, temp in enumerate(temperatures):
            while stack and temperatures[stack[-1]] < temp:
                j = stack.pop()
                result[j] = i - j
            stack.append(i)

        return result
```
