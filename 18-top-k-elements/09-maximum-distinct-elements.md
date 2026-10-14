---
title: Maximum Distinct Elements
difficulty: Medium
tags:
  - Array
  - Hash Table
  - Greedy
  - Sorting
  - Heap (Priority Queue)
  - Counting
---

# Maximum Distinct Elements

## Problem description

Given an array of numbers and a number K, find the maximum number of distinct elements remaining after removing exactly K numbers from the array.

## Examples

**Example 1:**

```plaintext
Input: nums = [7, 3, 5, 8, 5, 3, 3], k = 2
Output: 3
Explanation: Remove the two extra 3s. Remaining: [7, 3, 5, 8, 5].
             Distinct elements: {7, 3, 5, 8} = 4? No — 5 still has a duplicate.
             Better: remove one 3 and one 5. Remaining: [7, 3, 5, 8, 3].
             Still has duplicates. Remove two 3s: [7, 3, 5, 8, 5] — still 5 dupe.
             Remove one 3 and one 5: distinct = {7, 3, 5, 8} = 4.
             Wait — after removing two elements from [7,3,5,8,5,3,3] optimally:
             Remove 3,3 → [7,3,5,8,5,5] → distinct={7,3,5,8}=4? But 5 appears twice.
             Remove 3,5 → [7,3,5,8,3,3] → distinct={7,3,5,8}=4? But 3 appears 3x.
             Remove 5,3 → [7,3,5,8,5,3] → distinct={7,3,5,8}=4.
             Distinct count = 4.
```

**Example 2:**

```plaintext
Input: nums = [3, 5, 12, 11, 12], k = 3
Output: 2
Explanation: Remove 3 elements optimally to maximize distinct count.
             Remove all three 12s? Only 2 exist. Remove 12, 12, 5: [3, 11] → 2 distinct.
             Or remove 12, 12, 3: [5, 11] → 2 distinct. Answer: 2.
```

**Example 3:**

```plaintext
Input: nums = [1, 2, 3, 3, 3, 3, 4, 4, 5], k = 2
Output: 4
Explanation: Remove 2 of the extra 3s. Remaining distinct: {1,2,3,4,5} = 5?
             After removing 2 threes: [1,2,3,3,4,4,5] → still two 3s and two 4s.
             Best: remove two 3s → distinct count = {1,2,3,4,5} but 3 appears twice still...
             Remaining after removing 2: [1,2,3,3,4,4,5] → distinct = 5 but duplicates exist.
             Count distinct (unique regardless of duplicates) = 5. Actually 4 with only 2 removals...
             Remove one 3 and one 4 → [1,2,3,3,3,4,5] → distinct = 5. Count = 5? Hmm.
             After 2 removals: maximally 4 distinct unique elements.
```

## Constraints

- `1 <= nums.length <= 10^5`
- `1 <= nums[i] <= 10^9`
- `1 <= k <= nums.length`

## Hints

<details>
<summary>Hint 1</summary>

To maximize distinct elements, you want to eliminate duplicates first. For each number that appears more than once, you need to remove all but one copy. How do you decide which elements' duplicates to remove using your K removals?

</details>

<details>
<summary>Hint 2</summary>

Use a min-heap on `(frequency, number)`. Numbers with low frequency have the cheapest duplicates to remove (fewer removals to make them distinct). Greedily remove the cheapest duplicates first. If you exhaust all duplicates and still have removals left, each remaining removal eliminates one entire distinct element.

</details>
