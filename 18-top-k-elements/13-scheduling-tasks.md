---
title: Scheduling Tasks
difficulty: Medium
leetcode_title: Task Scheduler
leetcode: https://leetcode.com/problems/task-scheduler/
tags:
  - Array
  - Hash Table
  - Greedy
  - Sorting
  - Heap (Priority Queue)
  - Counting
---

# Scheduling Tasks

## Problem description

Given a list of tasks (each represented by a letter) and a cooldown interval `n`, find the minimum number of intervals required to execute all tasks. The CPU must wait at least `n` intervals between two executions of the same task. The CPU may be idle during this wait.

## Examples

**Example 1:**

```plaintext
Input: tasks = ["a", "a", "a", "b", "b", "b"], n = 2
Output: 8
Explanation: A -> B -> idle -> A -> B -> idle -> A -> B
             Total intervals: 8
```

**Example 2:**

```plaintext
Input: tasks = ["a", "a", "a", "b", "b", "b"], n = 0
Output: 6
Explanation: No cooldown needed. Any order works. Total intervals: 6.
```

**Example 3:**

```plaintext
Input: tasks = ["a", "a", "a", "a", "a", "a", "b", "c", "d", "e", "f", "g"], n = 2
Output: 16
Explanation: A -> B -> C -> A -> D -> E -> A -> F -> G -> A -> idle -> idle -> A -> idle -> idle -> A
```

## Constraints

- `1 <= tasks.length <= 10^4`
- `tasks[i]` is an uppercase English letter.
- `0 <= n <= 100`

## Hints

<details>
<summary>Hint 1</summary>

The most frequent task determines the minimum time. If task A appears `f` times with cooldown `n`, you need at least `(f - 1) * (n + 1) + 1` intervals just for A. Can you see why?

</details>

<details>
<summary>Hint 2</summary>

Alternatively, simulate with a max-heap and a cooldown queue (like Rearrange String K Distance Apart). At each step, pick the most frequent available task. If no task is available, be idle. Track the real time and the cooldown release time for each task.

</details>
