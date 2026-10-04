---
title: Reverse a Linked List
difficulty: Easy
leetcode_title: Reverse Linked List
leetcode: https://leetcode.com/problems/reverse-linked-list/
tags:
  - Linked List
  - Recursion
---

# Reverse a Linked List

## Problem description

Given the `head` of a singly linked list, reverse the list, and return the reversed list.

## Examples

**Example 1:**

```plaintext
Input: head = [1,2,3,4,5]
Output: [5,4,3,2,1]
```

**Example 2:**

```plaintext
Input: head = [1,2]
Output: [2,1]
```

**Example 3:**

```plaintext
Input: head = []
Output: []
```

## Constraints

- The number of nodes in the list is the range `[0, 5000]`.
- `-5000 <= Node.val <= 5000`

## Hints

<details>
<summary>Hint 1</summary>

You only need to flip the direction of every `next` pointer. The danger is that flipping one destroys your only route to the rest of the list.

</details>

<details>
<summary>Hint 2</summary>

Save the next node *before* you redirect the current one. Three pointers — previous, current, next — are enough.

</details>

## Solution

### Intuition

To reverse a linked list in-place, we need to flip each node's `next` pointer to point backward instead of forward.

The key insight is that we can do this in a single pass by maintaining three pointers: one for the current node, one for the previous node (which becomes the new "next"), and one to save the original next before we overwrite it.

```plaintext
Original:   1 ──▶ 2 ──▶ 3 ──▶ null
            ▲
           curr, prev = null

Step 1 — save next=2, flip 1.next → null, advance:

null ◀── 1     2 ──▶ 3 ──▶ null
         ▲     ▲
        prev  curr

Step 2 — save next=3, flip 2.next → 1, advance:

null ◀── 1 ◀── 2     3 ──▶ null
               ▲     ▲
              prev  curr

Step 3 — save next=null, flip 3.next → 2, advance:

null ◀── 1 ◀── 2 ◀── 3     null
                     ▲     ▲
                    prev  curr

curr is null → loop ends, return prev (node 3)

Result:    3 ──▶ 2 ──▶ 1 ──▶ null
```

### Algorithm

1. Initialize `previous` to `null` and `current` to `head`
2. While `current` is not null:
   - Save `current.next` in a temp variable
   - Point `current.next` to `previous` (reverse the link)
   - Move `previous` to `current`
   - Move `current` to the saved next
3. Return `previous` (the new head)

### Complexity analysis

- Time complexity: $O(n)$ - single pass through the list
- Space complexity: $O(1)$ - only using three pointers

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
            next_node = current_node.next
            
            current_node.next = previous_node
            previous_node = current_node

            current_node = next_node
        
        return previous_node
```
