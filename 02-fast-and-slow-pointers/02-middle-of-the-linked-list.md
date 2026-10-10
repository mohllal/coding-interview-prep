---
title: Middle of the Linked List
difficulty: Easy
leetcode: https://leetcode.com/problems/middle-of-the-linked-list/
tags:
  - Linked List
  - Two Pointers
---

# Middle of the Linked List

## Problem description

Given the `head` of a singly linked list, return the middle node of the linked list.

If there are two middle nodes, return **the second middle** node.

## Examples

**Example 1:**

```plaintext
1 ──▶ 2 ──▶ [3] ──▶ 4 ──▶ 5 ──▶ null
             ▲
             │
           middle

Input: head = [1,2,3,4,5]
Output: [3,4,5]
Explanation: The middle node of the list is node 3.
```

**Example 2:**

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ [4] ──▶ 5 ──▶ 6 ──▶ null
                   ▲
                   │
                 middle

Input: head = [1,2,3,4,5,6]
Output: [4,5,6]
Explanation: Since the list has two middle nodes with values 3 and 4, we return the second one.
```

## Constraints

- The number of nodes in the list is in the range `[1, 100]`.
- `1 <= Node.val <= 100`

## Hints

<details>
<summary>Hint 1</summary>

Counting the nodes first and then walking half of them works, but it takes two passes. One pass is possible.

</details>

<details>
<summary>Hint 2</summary>

When a pointer moving two steps at a time reaches the end, a pointer moving one step is exactly halfway. Decide carefully what your loop condition should be for even lengths.

</details>

## Solution

### Intuition

Since fast moves twice as fast as slow, when fast reaches the end, slow has traveled exactly half the distance—placing it at the middle. For even-length lists, the loop condition `fast.next is not None` ensures slow lands on the second middle node.

**Odd length:** fast stops on the last node:

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null

             slow   fast
start          1      1
iteration 1    2      3
iteration 2    3      5      fast.next is null → stop

slow = 3
```

**Even length:** fast steps past the last node to `null`:

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ 6 ──▶ null

             slow   fast
start          1      1
iteration 1    2      3
iteration 2    3      5
iteration 3    4      null   fast is null → stop

slow = 4 (the second of the two middles, 3 and 4)
```

### Algorithm

1. Initialize slow and fast pointers at head
2. Move slow one step, fast two steps each iteration
3. Stop when fast reaches the end (null or no next node)
4. Return slow—it's at the middle

### Complexity analysis

- Time complexity: $O(n)$ - Single pass through the list
- Space complexity: $O(1)$ - Only two pointers used

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def middle_node(self, head: Optional[ListNode]) -> Optional[ListNode]:
        fast = head
        slow = head

        while fast is not None and fast.next is not None:
            fast = fast.next.next
            slow = slow.next

        return slow
```

## Returning the first middle instead

If the problem asks for the **first** of the two middle nodes on an even length, start fast one node ahead. The loop doesn't change:

```python
slow, fast = head, head.next

while fast is not None and fast.next is not None:
    fast = fast.next.next
    slow = slow.next
```

Why this works: fast is now always one node further along, so it reaches the end one step sooner, and slow stops one step earlier.

- **Even length**: in the original, fast's final jump takes it past the last node to `null`, and slow follows with one extra step onto the second middle. Starting ahead, fast lands on the last node instead, the loop ends, and slow stays on the first middle.
- **Odd length**: starting ahead, fast jumps past the end one step sooner, but slow still stops on the only middle.

```plaintext
1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ 6 ──▶ null

                fast = head                fast = head.next
start           slow=1  fast=1             slow=1  fast=2
iteration 1     slow=2  fast=3             slow=2  fast=4
iteration 2     slow=3  fast=5             slow=3  fast=6   (6.next is null, stop)
iteration 3     slow=4  fast=null
result          4  (second middle)         3  (first middle)

1 ──▶ 2 ──▶ 3 ──▶ 4 ──▶ 5 ──▶ null

result          3                          3  (same on odd lengths)
```

One thing to watch: `head.next` crashes on an empty list. Guard against `head is None` if the input can be empty.

The first middle is what you want when **splitting** a list into halves (merge sort, comparing halves): slow becomes the tail of the first half, so `second = slow.next; slow.next = None` cuts the list cleanly, and on even lengths both halves are the same size.
