---
title: Palindrome Linked List
difficulty: Easy
leetcode: https://leetcode.com/problems/palindrome-linked-list/
tags:
  - Linked List
  - Two Pointers
  - Stack
  - Recursion
---

# Palindrome Linked List

## Problem description

Given the head of a singly linked list, write a method to check if the linked list is a palindrome or not.

Your algorithm should use constant space and the input linked list should be in the original form once the algorithm is finished. The algorithm should have $O(n)$ time complexity where `n` is the number of nodes in the linked list.

## Examples

**Example 1:**

```plaintext
2 ──▶ 4 ──▶ 6 ──▶ 4 ──▶ 2 ──▶ null

Input: head = [2,4,6,4,2]
Output: true
Explanation: Reads the same forwards and backwards.
```

**Example 2:**

```plaintext
2 ──▶ 4 ──▶ 6 ──▶ 4 ──▶ 2 ──▶ 2 ──▶ null

Input: head = [2,4,6,4,2,2]
Output: false
```

## Constraints

- The number of nodes in the list is in the range `[1, 10⁵]`.
- `0 <= Node.val <= 9`

## Hints

<details>
<summary>Hint 1</summary>

Copying the values into an array makes this trivial but costs $O(n)$ space. The constraint is to do it in $O(1)$.

</details>

<details>
<summary>Hint 2</summary>

Find the middle, reverse the second half in place, then compare the two halves node by node. Consider whether you should restore the list afterwards.

</details>

## Solution

### Intuition

To achieve $O(1)$ space, we reverse the second half of the list in-place. Then we compare the first half with the reversed second half. Finally, we restore the list by reversing the second half again.

The odd/even length needs no special handling. The middle node is where the reversal starts: the center for odd lengths, the second middle for even lengths. Comparison runs only while the reversed half has nodes.

**Odd length:**

```plaintext
1. Find the middle — slow stops on the center node

   2 ──▶ 4 ──▶ 6 ──▶ 4 ──▶ 2 ──▶ null
               ▲
             middle

2. Reverse from the middle onward

   head                     reversed head
    │                             │
    ▼                             ▼
    2 ──▶ 4 ──▶ 6 ◀── 4 ◀──────── 2
                │
                ▼
               null

3. Compare, p1 from head and p2 from the reversed head, until p2 runs out

   p1:  2 ──▶ 4 ──▶ 6
   p2:  2 ──▶ 4 ──▶ 6
        ✓     ✓     ✓   → palindrome

   Both halves end on the same center node, so it's compared with itself —
   always equal, so it never affects the result.

4. Reverse the second half again to restore the list

   2 ──▶ 4 ──▶ 6 ──▶ 4 ──▶ 2 ──▶ null
```

**Even length:**

```plaintext
1. Find the middle — slow stops on the second of the two middle nodes

   1 ──▶ 2 ──▶ 2 ──▶ 1 ──▶ null
               ▲
             middle

2. Reverse from the middle onward

   head               reversed head
    │                       │
    ▼                       ▼
    1 ──▶ 2 ──▶ 2 ◀──────── 1
                │
                ▼
               null

3. Compare, p1 from head and p2 from the reversed head, until p2 runs out

   p1:  1 ──▶ 2 ──▶ 2
   p2:  1 ──▶ 2
        ✓     ✓         → palindrome (p2 is done; p1's last node is never compared)

4. Reverse the second half again to restore the list

   1 ──▶ 2 ──▶ 2 ──▶ 1 ──▶ null
```

### Algorithm

1. Find middle node using fast/slow pointers
2. Reverse the second half of the list
3. Compare first half with reversed second half
4. Reverse second half again to restore original list
5. Return result

### Complexity analysis

- Time complexity: $O(n)$ - Multiple passes but still linear
- Space complexity: $O(1)$ - Only pointers, no extra data structures

```python
class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next

class Solution:
    def reverse_list(self, head: Optional[ListNode]) -> Optional[ListNode]:
        previous_node = None
        current_node = head

        while current_node is not None:
            next_node = current_node.next # save next node
            current_node.next = previous_node # reverse current node
    
            previous_node = current_node # update previous node
            current_node = next_node # move to next node

        return previous_node

    def get_middle_node(self, head: Optional[ListNode]) -> Optional[ListNode]:
        slow = head
        fast = head

        while fast is not None and fast.next is not None:
            slow = slow.next
            fast = fast.next.next

        return slow

    def is_palindrome(self, head: Optional[ListNode]) -> bool:
        if head is None or head.next is None:
            return True
        
        second_half = self.get_middle_node(head)
        reversed_second_half = self.reverse_list(second_half)

        p1 = head
        p2 = reversed_second_half
        result = True

        while p2 is not None:
            if p1.val != p2.val:
                result = False
                break

            p1 = p1.next
            p2 = p2.next

        self.reverse_list(reversed_second_half)
        return result
```
