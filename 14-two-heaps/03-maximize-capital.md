---
title: Maximize Capital
difficulty: Hard
leetcode_title: IPO
leetcode: https://leetcode.com/problems/ipo/
tags:
  - Array
  - Greedy
  - Sorting
  - Heap (Priority Queue)
---

# Maximize Capital

## Problem description

Given a set of investment projects with their respective profits, find the most profitable projects to invest in. You are given an initial capital and may invest in at most a fixed number of projects. Return the maximum total capital after selecting the most profitable projects.

- A project can only be started when your current capital is at least its required capital.
- After completing a project, you get your capital back **plus** its profit.
- Each project can be selected at most once.

## Examples

**Example 1:**

```plaintext
Input: capital = [0, 1, 2], profits = [1, 2, 3], initial_capital = 1, number_of_projects = 2
Output: 6
Explanation:
1. With capital 1, start project 1 (needs 1), earning 2 → capital becomes 3.
2. With capital 3, start project 2 (needs 2), earning 3 → capital becomes 6.
```

**Example 2:**

```plaintext
Input: capital = [0, 1, 2, 3], profits = [1, 2, 3, 5], initial_capital = 0, number_of_projects = 3
Output: 8
Explanation:
1. With capital 0, only project 0 is affordable, earning 1 → capital becomes 1.
2. With capital 1, start project 1, earning 2 → capital becomes 3.
3. With capital 3, start project 3, earning 5 → capital becomes 8.
```

## Constraints

- `1 <= number_of_projects <= 10^5`
- `0 <= initial_capital <= 10^9`
- `n == profits.length == capital.length`
- `1 <= n <= 10^5`
- `0 <= profits[i] <= 10^4`
- `0 <= capital[i] <= 10^9`
- Neither array is sorted.
