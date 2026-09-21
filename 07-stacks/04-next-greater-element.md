---
title: Next Greater Element
difficulty: Easy
leetcode_title: Next Greater Element I
leetcode: https://leetcode.com/problems/next-greater-element-i/
tags:
  - Array
  - Hash Table
  - Stack
  - Monotonic Stack
---

# Next Greater Element

## Problem description

The next greater element of some element `x` in an array is the **first greater** element that is **to the right** of `x` in the same array.

You are given two **distinct 0-indexed** integer arrays `nums1` and `nums2`, where `nums1` is a subset of `nums2`.

For each `0 <= i < nums1.length`, find the index `j` such that `nums1[i] == nums2[j]` and determine the next greater element of `nums2[j]` in `nums2`. If there is no next greater element, then the answer for this query is `-1`.

Return an array `ans` of length `nums1.length` such that `ans[i]` is the next greater element as described above.

## Examples

**Example 1:**

```plaintext
Input: nums1 = [4,1,2], nums2 = [1,3,4,2]
Output: [-1,3,-1]
Explanation:
- 4 is at index 2 in nums2; no greater element to its right → -1
- 1 is at index 0 in nums2; next greater is 3 → 3
- 2 is at index 3 in nums2; no greater element to its right → -1
```

**Example 2:**

```plaintext
Input: nums1 = [2,4], nums2 = [1,2,3,4]
Output: [3,-1]
```

## Constraints

- `1 <= nums1.length <= nums2.length <= 1000`
- `0 <= nums1[i], nums2[i] <= 10⁴`
- All integers in `nums1` and `nums2` are unique
- All integers of `nums1` also appear in `nums2`

## Hints

<details>
<summary>Hint 1</summary>

Scanning forward from each element is $O(n^2)$. Notice that once an element has found its answer, it is never needed again.

</details>

<details>
<summary>Hint 2</summary>

Keep a stack of elements still waiting for a larger value. When a new value arrives, it resolves everything smaller sitting on top.

</details>

## Solution

### Intuition

Instead of searching to the right for every element `(O(n²))`, process `nums2` once using a decreasing monotonic stack to find the next greater element for each number in `nums2` in a single pass.

Process from right to left: the stack maintains candidates that could be "next greater" for elements to the left.

### Algorithm

1. Initialize a hash map with all `nums2` values mapped to `-1`
2. Traverse `nums2` from right to left:
   - Pop elements from stack that are ≤ current (they can't be "next greater" for anything)
   - If stack is non-empty, the top is the next greater element
   - Push current element onto stack
3. Look up each `nums1` element in the hash map

### Complexity analysis

- Time complexity: $O(n + m)$ — each element pushed/popped at most once
- Space complexity: $O(n)$ — stack and hash map for `nums2`

The nested `while` inside the `for` loop looks like $O(n^2)$, but it is not. Each element enters the stack exactly once (one push per outer iteration) and leaves at most once (one pop, whenever a larger element arrives). The total number of push and pop operations across the entire loop is therefore at most $2n$, regardless of how those operations are distributed.

The worst case concentrates many pops into a single iteration. For `nums2 = [10, 1, 2, 3, 4, 5]`, processing right to left, the first five iterations each push without popping — the stack grows to `[5, 4, 3, 2, 1]`. When `10` is processed, the `while` pops all five elements in one go:

```plaintext
process 5  →  push 5          stack: [5]
process 4  →  push 4          stack: [5, 4]
process 3  →  push 3          stack: [5, 4, 3]
process 2  →  push 2          stack: [5, 4, 3, 2]
process 1  →  push 1          stack: [5, 4, 3, 2, 1]
process 10 →  pop 1,2,3,4,5   stack: []   (5 pops in one while loop)
              push 10          stack: [10]

total: 6 pushes + 5 pops = 11 operations for n = 6
```

The pops that cluster on that last step cannot happen again — those elements are gone. The total stays $O(n)$.

```python
class Solution:
    def next_greater_element(self, nums1: List[int], nums2: List[int]) -> List[int]:
        stack = []  # Monotonic decreasing stack
        next_greater = {num: -1 for num in nums2}

        for num in reversed(nums2):
            # Pop smaller elements, they can't be "next greater" for anything to the left
            while stack and stack[-1] <= num:
                stack.pop()

            if stack:
                next_greater[num] = stack[-1]  # The top of the stack is the next greater element

            stack.append(num)

        return [next_greater[num] for num in nums1]
```
