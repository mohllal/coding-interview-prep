---
title: Cycle in a Circular Array
difficulty: Medium
leetcode_title: Circular Array Loop
leetcode: https://leetcode.com/problems/circular-array-loop/
tags:
  - Array
  - Hash Table
  - Two Pointers
  - Floyd's Cycle Finding Algorithm
---

# Cycle in a Circular Array

## Problem description

You are playing a game involving a circular array of non-zero integers `nums`. Each `nums[i]` denotes the number of indices forward/backward you must move if you are located at index `i`:

- If `nums[i]` is positive, move `nums[i]` steps forward
- If `nums[i]` is negative, move `nums[i]` steps backward

Since the array is circular, you may assume that moving forward from the last element puts you on the first element, and moving backwards from the first element puts you on the last element.

A **cycle** in the array consists of a sequence of indices `seq` of length `k` where:

- Following the movement rules above results in the repeating index sequence
- All `nums[seq[j]]` are either all positive or all negative (same direction)
- `k > 1` (cycle length must be greater than 1)

Return `true` if there is a cycle in `nums`, or `false` otherwise.

## Examples

**Example 1:**

```plaintext
Input: nums = [2, -1, 1, 2, 2]

index     0    1    2    3    4
nums      2   -1    1    2    2
jumps to  2    0    3    0    1        next = (i + nums[i]) mod 5

starting at 0:

   ┌─── (+2) wraps to 0 ───┐
   ▼                       │
   0 ──(+2)──▶ 2 ──(+1)──▶ 3

Output: true
Explanation: 0 → 2 → 3 → 0 is a cycle of length 3, and every jump is forward.
```

**Example 2:**

```plaintext
Input: nums = [-1, -2, -3, -4, -5, 6]

index     0    1    2    3    4    5
nums     -1   -2   -3   -4   -5    6
jumps to  5    5    5    5    5    5        next = (i + nums[i]) mod 6

   0 ─┐
   1 ─┤
   2 ─┼──(backward)──▶ 5 ──┐
   3 ─┤                ▲   │ (+6) goes all the way round
   4 ─┘                └───┘      and lands back on 5

Output: false
Explanation: Every path ends at index 5. Its only loop is 5 → 5, a self-loop of length 1, which doesn't count. Reaching 5 from any other index switches from backward to forward, which isn't allowed either.
```

**Example 3:**

```plaintext
Input: nums = [1, -1, 5, 1, 4]

index     0    1    2    3    4
nums      1   -1    5    1    4
jumps to  1    0    2    4    3        next = (i + nums[i]) mod 5

   0 ──(+1)──▶ 1 ──(-1)──▶ 0      mixed directions        ✗
   2 ──(+5)──▶ 2                  self-loop, length 1     ✗
   3 ──(+1)──▶ 4 ──(+4)──▶ 3      forward, length 2       ✓

Output: true
Explanation: 3 → 4 → 3 is a valid cycle: length 2, and both jumps are forward. The other two loops are invalid, but one valid cycle is enough.
```

## Constraints

- `1 <= nums.length <= 5000`
- `-1000 <= nums[i] <= 1000`
- `nums[i] != 0`

## Hints

<details>
<summary>Hint 1</summary>

A valid cycle has extra requirements beyond simply revisiting an index: it must have length greater than one and never change direction.

</details>

<details>
<summary>Hint 2</summary>

Run the fast and slow pointers from each starting index, aborting the moment the direction flips or a single-element loop appears. Indices proven bad can be remembered so they are not retried.

</details>

## Solution 1: Two passes, one per direction

### Intuition

A valid cycle moves in only one direction, so look for forward-only cycles and backward-only cycles separately.

The first pass tries every index with a positive value as a starting point. From each one, it runs fast and slow pointers that are only allowed to make forward moves. The second pass does the same for every index with a negative value, allowing only backward moves.

A walk is abandoned and stop the search the moment it breaks a rule:

- Direction change: it lands on a value pointing the other way.
- Self-loop: an index jumps to itself, a cycle of length 1, which doesn't count.

If the pointers meet without breaking either rule, they're inside a valid cycle.

Every start has to be tried, because a valid cycle may not be reachable from the first index you pick:

```plaintext
nums = [1, -1, 5, 1, 4]

forward pass (starts with a positive value: 0, 2, 3, 4)

  start 0:  0 ──(+1)──▶ 1      1 points backward          → abandon
  start 2:  2 ──(+5)──▶ 2      self-loop                  → abandon
  start 3:  3 ──(+1)──▶ 4 ──(+4)──▶ 3   pointers meet      → valid cycle ✓
```

### Algorithm

1. Forward pass: for every index with a positive value, run fast and slow pointers using forward moves only. Return `true` if they meet.
2. Backward pass: for every index with a negative value, do the same with backward moves only.
3. On each single move, stop that walk if the value points the other way or the next index is the current one.
4. If neither pass finds a meeting, return `false`.

### Complexity analysis

- Time complexity: $O(n^2)$ - each of the $n$ starting points can walk up to $O(n)$ steps, and different starts may re-walk the same path
- Space complexity: $O(1)$ - only a few indices are stored

```python
class Solution:
    def get_next_index(self, nums: List[int], index: int, forward: bool) -> int:
        if (nums[index] > 0) != forward:  # abandon rule 1: this move goes the other way
            return -1

        next_index = (index + nums[index]) % len(nums)
        if next_index == index:  # abandon rule 2: self-loop, cycle of length 1
            return -1

        return next_index

    def has_cycle_from(self, nums: List[int], start: int, forward: bool) -> bool:
        slow = fast = start

        while True:
            slow = self.get_next_index(nums, slow, forward)
            fast = self.get_next_index(nums, fast, forward)
            if fast != -1:
                fast = self.get_next_index(nums, fast, forward)

            if slow == -1 or fast == -1:
                return False

            if slow == fast:
                return True

    def circular_array_loop(self, nums: List[int]) -> bool:
        # pass 1: cycles made only of forward moves
        for start in range(len(nums)):
            if nums[start] > 0 and self.has_cycle_from(nums, start, forward=True):
                return True

        # pass 2: cycles made only of backward moves
        for start in range(len(nums)):
            if nums[start] < 0 and self.has_cycle_from(nums, start, forward=False):
                return True

        return False
```

## Solution 2: Using linked list representation

### Intuition

Think of the array as a linked list where each index points to its next index based on the value. We can then use cycle detection similar to [Linked List Cycle](./01-linked-list-cycle.md).

However, we need additional checks:

1. Cycle must have length > 1 (no self-loops)
2. All elements in cycle must have same direction (all positive or all negative)

### Algorithm

1. Build a linked list representation of the array
2. For each starting index, run cycle detection with direction checking
3. Return true if a valid cycle is found

### Complexity analysis

- Time complexity: $O(n^2)$ - For each starting point, we may traverse the entire array
- Space complexity: $O(n)$ - Storing the linked list nodes

```python
class ListNode:
    def __init__(self, index: int, is_forward: bool):
        self.index = index
        self.is_forward = is_forward
        self.next = None

class Solution:
    def get_next_index(self, nums: List[int], index: int) -> int:
        return (index + nums[index]) % len(nums)

    def is_self_loop(self, node: ListNode) -> bool:
        return node.next is node

    def has_valid_cycle_from(self, start: ListNode) -> bool:
        slow = start
        fast = start

        while True:
            # a node pointing to itself is a cycle of length 1, which doesn't count
            if self.is_self_loop(slow) or self.is_self_loop(fast) or self.is_self_loop(fast.next):
                return False

            # every move in the cycle must go the same way
            if not (slow.is_forward == fast.is_forward == fast.next.is_forward):
                return False

            slow = slow.next
            fast = fast.next.next

            if slow is fast:
                return True

    def circular_array_loop(self, nums: List[int]) -> bool:
        nodes = [ListNode(index, nums[index] > 0) for index in range(len(nums))]

        for node in nodes:
            node.next = nodes[self.get_next_index(nums, node.index)]

        for node in nodes:
            if self.has_valid_cycle_from(node):
                return True

        return False
```

## Solution 3: In-place with marking

### Intuition

Solution 1 is $O(n^2)$ because different starting points re-walk the same paths. This version remembers which indices are dead ends, so each one is walked only once.

The key idea: if a walk from some start fails, every index it passed through (in the same direction) fails too. Starting from any of them just replays the rest of that same walk and hits the same problem:

```plaintext
walk from a:   a ──▶ b ──▶ c ──▶ d     d points the other way ✗

start at b:          b ──▶ c ──▶ d     the same tail, the same failure ✗
start at c:                c ──▶ d     ✗
```

So after a failed walk, overwrite every index on it with `0`. A `0` works as a "dead end" marker for free: a value of `0` would mean jumping to yourself, which already counts as invalid, so any later walk that reaches it stops right there.

Marking stops at the first index that points the **other** way (`d` above). That index wasn't proven bad: it belongs to the opposite direction and still gets its own turn as a starting point.

Example 3, `nums = [1, -1, 5, 1, 4]`, showing the array after each start:

```plaintext
index:        0    1    2    3    4
start:      [ 1,  -1,   5,   1,   4 ]

start 0 (forward)
  0 ──▶ 1          1 points backward → no cycle
  mark 0      [ 0,  -1,   5,   1,   4 ]      1 is kept: it's a backward start

start 1 (backward)
  1 ──▶ 0          0 is a dead end → no cycle
  mark 1      [ 0,   0,   5,   1,   4 ]

start 2 (forward)
  2 ──▶ 2          self-loop → no cycle
  mark 2      [ 0,   0,   0,   1,   4 ]

start 3 (forward)
  3 ──▶ 4 ──▶ 3    slow and fast meet → valid cycle ✓

return true
```

### Algorithm

1. For each index, skip it if it's already marked `0`.
2. Otherwise run fast and slow pointers from it, using only moves in the start's direction. Stop the walk on a direction change, a self-loop, or a `0`.
3. If the pointers meet, return `true`.
4. If the walk failed, walk the same path again, setting each index to `0` until reaching a `0` or an index that points the other way.
5. If no start finds a cycle, return `false`.

### Complexity analysis

- Time complexity: $O(n)$ - every index is marked at most once, and a marked index is never walked again, so each index is visited only a constant number of times overall
- Space complexity: $O(1)$ - the markers are written into `nums` itself. This modifies the input; copy it first if the caller needs it unchanged

```python
class Solution:
    def get_next_index(self, nums: List[int], index: int, is_forward: bool) -> int:
        # -1 means this walk can't be part of a valid cycle
        if nums[index] == 0 or (nums[index] > 0) != is_forward:
            return -1

        next_index = (index + nums[index]) % len(nums)
        if next_index == index:  # self-loop, cycle of length 1
            return -1

        return next_index

    def has_cycle_from(self, nums: List[int], start: int) -> bool:
        is_forward = nums[start] > 0
        slow = fast = start

        while True:
            slow = self.get_next_index(nums, slow, is_forward)
            fast = self.get_next_index(nums, fast, is_forward)
            if fast != -1:
                fast = self.get_next_index(nums, fast, is_forward)

            if slow == -1 or fast == -1:
                return False

            if slow == fast:
                return True

    def mark_path_as_dead_end(self, nums: List[int], start: int) -> None:
        is_forward = nums[start] > 0
        index = start

        while nums[index] != 0 and (nums[index] > 0) == is_forward:
            next_index = (index + nums[index]) % len(nums)
            nums[index] = 0
            index = next_index

    def circular_array_loop(self, nums: List[int]) -> bool:
        for start in range(len(nums)):
            if nums[start] == 0:  # already proven to be a dead end
                continue

            if self.has_cycle_from(nums, start):
                return True

            self.mark_path_as_dead_end(nums, start)

        return False
```
