---
title: Start of Linked List Cycle
difficulty: Medium
leetcode_title: Linked List Cycle II
leetcode: https://leetcode.com/problems/linked-list-cycle-ii/
tags:
  - Hash Table
  - Linked List
  - Two Pointers
  - Floyd's Cycle Finding Algorithm
---

# Start of Linked List Cycle

## Problem description

Given the `head` of a linked list, return the node where the cycle begins. If there is no cycle, return `null`.

There is a cycle in a linked list if there is some node in the list that can be reached again by continuously following the `next` pointer. Internally, `pos` is used to denote the index of the node that tail's `next` pointer is connected to (**0-indexed**). It is `-1` if there is no cycle. **Note that** `pos` **is not passed as a parameter**.

**Do not modify** the linked list.

## Examples

**Example 1:**

```plaintext
3 ──▶ [2] ──▶ 0 ──▶ -4
       ▲             │
       └─────────────┘

Input: head = [3,2,0,-4], pos = 1
Output: tail connects to node index 1
Explanation: There is a cycle in the linked list, where tail connects to the second node.
```

**Example 2:**

```plaintext
┌───────────┐
▼           │
[1] ──▶ 2 ──┘

Input: head = [1,2], pos = 0
Output: tail connects to node index 0
Explanation: There is a cycle in the linked list, where tail connects to the first node.
```

**Example 3:**

```plaintext
1 ──▶ null

Input: head = [1], pos = -1
Output: no cycle
Explanation: There is no cycle in the linked list.
```

## Constraints

- The number of the nodes in the list is in the range `[0, 10^4]`.
- `-10^5 <= Node.val <= 10^5`
- `pos` is `-1` or a valid index in the linked-list.

## Hints

<details>
<summary>Hint 1</summary>

Finding *that* a cycle exists is the easy half. The meeting point is not the cycle's start — but it is not arbitrary either.

</details>

<details>
<summary>Hint 2</summary>

Write down the distances: head to cycle start, cycle start to meeting point, and the rest of the loop. Two equal expressions fall out, and they tell you where to restart one pointer.

</details>

## Solution 1: Using a hash set

### Intuition

The simplest approach: if we've seen a node before, it must be the start of the cycle. We traverse the list and store each visited node. The first node we encounter twice is where the cycle begins.

### Algorithm

1. Create a set to track visited nodes
2. Traverse the list, checking if each node exists in the set
3. If found, return it (cycle start)
4. If we reach null, no cycle exists

### Complexity analysis

- Time complexity: $O(n)$ - Single pass through the list
- Space complexity: $O(n)$ - Storing visited nodes

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def detect_cycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        visited = set()
        current = head

        while current is not None:
            if current in visited:
                return current

            visited.add(current)
            current = current.next

        return None
```

## Solution 2: Floyd's algorithm

### Intuition

Floyd's cycle detection gives us a meeting point inside the cycle. The key insight is that the distance from head to cycle start equals the remaining distance from the meeting point to the cycle start.

#### Setup and variables

```plaintext
                 ┌─────── k - b ────────┐
                 ▼   loop length = k    │
head ─── d ───▶ [S] ─────── b ───────▶ [M]

[S]    cycle start
[M]    meeting point
d      steps from head to [S]
b      steps from [S] forward to [M]
k - b  steps from [M] forward, around the loop, back to [S]
k      cycle length = b + (k - b)
```

#### The math

When slow and fast meet at [M]:

- Slow traveled: `d + b` steps
- Fast traveled: `d + b + k` steps (completed one extra cycle)

Since fast moves twice as fast as slow:

$$2(d + b) = d + b + k$$

Both sides count the same thing — how far fast has walked when they meet — in two different ways:

- **By path:** fast walked the tail `d`, one lap `k`, then `b` to reach `[M]` → `d + b + k`.
- **By speed:** fast always walks twice what slow has → `2(d + b)`.

Both are true, so they're equal. In the example below (`d = 2`, `b = 2`, `k = 4`), they meet after 4 iterations: slow walked 4, fast walked 8, and `d + b + k = 8`.

Expanding and simplifying the equation:

$$2d + 2b = d + b + k$$
$$d = k - b$$

**What does `d = k - b` mean?**

- `k - b` = remaining distance from meeting point `[M]` back to cycle start `[S]`. Why: a full lap starting at `[S]` is `k` single steps. The first `b` of them take you from `[S]` to `[M]`, so the remaining `k - b` steps take you from `[M]` onward around the loop and back to `[S]`.
- `d` = distance from head to cycle start `[S]`

**These are equal!** So if we start one pointer at head and another at meeting point `[M]`, moving both one step at a time, they'll travel the same distance and meet at `[S]` (cycle start).

#### Example walkthrough

```plaintext
1 ──▶ 2 ──▶ [3] ──▶ 4 ──▶ [5] ──▶ 6
             ▲                    │
             └────────────────────┘

[S] = 3, and the pointers meet at [M] = 5

k     = 4   the loop is 3 → 4 → 5 → 6 → back to 3
b     = 2   3 → 4 → 5
k - b = 2   5 → 6 → 3
d     = 2   1 → 2 → 3
```

#### 1. Find the meeting point

| Step | Slow | Fast |
| ---- | ---- | ---- |
| 0    | 1    | 1    |
| 1    | 2    | 3    |
| 2    | 3    | 5    |
| 3    | 4    | 3    |
| 4    | 5    | 5 ✓  |

They meet at node `[5]`. So `b = 2` (distance from `[3]` to `[5]`).

Verify: `d = k - b` → `2 = 4 - 2` ✓

#### 2. Find the cycle start

Reset one pointer to head, keep other at meeting point:

| Step | From Head | From Meeting Point |
| ---- | --------- | ------------------ |
| 0    | 1         | 5                  |
| 1    | 2         | 6                  |
| 2    | 3 ✓       | 3 ✓                |

After `d = 2` steps, both pointers meet at node `[3]` (cycle start).

### Algorithm

1. Use slow/fast pointers to detect cycle and find meeting point
2. Reset slow to head, keep fast at meeting point
3. Move both one step at a time
4. They meet at cycle start

### Complexity analysis

- Time complexity: $O(n)$ - Each node visited at most twice
- Space complexity: $O(1)$ - Only two pointers used

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def get_cycle_start(self, head: Optional[ListNode], meeting_point: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = meeting_point

        while slow != fast:
            slow = slow.next
            fast = fast.next

        return slow

    def detect_cycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

            if slow == fast:
                return self.get_cycle_start(head, slow)

        return None
```

## Solution 3: Using cycle length

### Intuition

If we know the cycle length `k`, we can find the start without any equations.

Give `pointer_2` a head start of exactly `k` steps, then move both pointers one step at a time. Since they move at the same speed, `pointer_2` stays **one full lap ahead** of `pointer_1` the whole time.

A full lap around the loop brings you back to where you started. So as soon as `pointer_1` walks into the loop, `pointer_2` — one lap ahead — is standing on the same node. The first loop node `pointer_1` walks into is the cycle start, so that's where they meet.

Before that, `pointer_1` is still outside the loop, where "one lap ahead" is just some other node further along, so they can't meet early.

```plaintext
                 ┌─── loop length = k ───┐
                 ▼                       │
head ─── d ───▶ [S] ─────────────────────┘

[S]  cycle start
d    steps from head to [S]
k    cycle length: steps to go once around the loop back to [S]
```

#### Example walkthrough

```plaintext
1 ──▶ 2 ──▶ [3] ──▶ 4 ──▶ 5 ──▶ 6
             ▲                  │
             └──────────────────┘

k = 4   one lap: 3 → 4 → 5 → 6 → 3
```

Give `pointer_2` a head start of `k = 4` steps, then move both one step at a time:

```plaintext
         pointer_1   pointer_2
start       1          5        pointer_2 walked 1 → 2 → 3 → 4 → 5
step 1      2          6
step 2      3          3        ← meet at the cycle start
```

Look at the full path each pointer has walked when they meet:

```plaintext
pointer_1:  1 → 2 → 3
pointer_2:  1 → 2 → 3 → 4 → 5 → 6 → 3
                   └── full lap ───┘
```

`pointer_2` walked exactly the same path as `pointer_1`, plus one lap that ended right back on 3. That's why they land on the same node, and why that node is the cycle start.

### Algorithm

1. Detect cycle using slow/fast pointers
2. Calculate cycle length `k` by traversing the cycle from meeting point
3. Place `pointer_1` at head, `pointer_2` `k` steps ahead from head
4. Move both one step at a time until they meet
5. The node where they meet is the start of the cycle

### Complexity analysis

- Time complexity: $O(n)$ - Multiple passes but still linear
- Space complexity: $O(1)$ - Only pointers used

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def detect_cycle(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

            if slow == fast:
                cycle_length = self.get_cycle_length(slow)
                return self.get_cycle_start(head, cycle_length)

        return None

    def get_cycle_length(self, head: Optional[ListNode]) -> int:
        current = head.next
        length = 1

        while current != head:
            current = current.next
            length += 1

        return length

    def get_cycle_start(self, head: Optional[ListNode], k: int) -> Optional[ListNode]:
        pointer_1 = head
        pointer_2 = head

        for _ in range(k):
            pointer_2 = pointer_2.next

        while pointer_1 != pointer_2:
            pointer_1 = pointer_1.next
            pointer_2 = pointer_2.next

        return pointer_1
```
