# Pattern: Two Pointers

## Overview

The two pointers technique uses two pointers (or indices) to traverse a data structure simultaneously.

Instead of using nested loops with time complexity $O(n^2)$, two pointers can often solve problems in a single pass with time complexity $O(n)$.

## Core idea

Each pointer moves at most $n$ times, and neither ever moves backwards. Two pointers each walking forward at most $n$ steps is $2n$ moves in total, which is $O(n)$ — so the whole traversal stays linear even though it looks like it is examining pairs.

What makes this sound is that every move must eliminate possibilities you will never need to revisit. In a sorted array, for instance, if the sum at `left` and `right` is too large, every pair involving that `right` is also too large, so moving `right` inward discards a whole row of candidates at once.

## Variations

### 1. Converging pointers (opposite ends)

Pointers start at opposite ends and move toward each other until they meet.

Typical movement:

- One pointer starts at the left
- One pointer starts at the right
- At each step, move exactly one pointer inward

```plaintext
Array: [1, 2, 3, 4, 5, 6, 7]
        │                 │
        ▼                 ▼
       left             right
       ───▶             ◀────
```

Use it when:

- Array is sorted
- Finding pairs with a certain criteria
- Decision depends on sum / comparison
- You want to reduce the range of possible solutions

### 2. Fast–slow pointers (same direction)

Both pointers start at the same end, one moves faster (or conditionally).

Think of it as a read pointer and a write pointer.

Typical movement:

- Both start at the same index
- `fast` always moves
- `slow` moves conditionally

```plaintext
Array: [1, 1, 2, 2, 3, 4, 4]
        │     │
        ▼     ▼
       slow  fast
       ───▶  ───▶
```

Use it when:

- In-place modification
- Removing / collapsing elements
- Partitioning based on condition
- Deduplication (removing duplicates)

For a deep dive and more focused practice problems on this variation, see [Fast & Slow Pointers (Hare & Tortoise)](../02-fast-and-slow-pointers/README.md).

### 3. Anchored + expanding window (fixed anchor)

Fix one element → solve a two-pointer problem on the rest of the array.

This is very common in k-Sum problems.

Typical movement:

- Outer loop fixes an anchor element
- Inner loop uses two pointers to find pairs with the anchor element
- Anchor moves → window resets

```plaintext
Array: [-2, 0, -1, 1, 3, 2]      target = 2
         │
         ▼
         i (anchor)
              │   │
              ▼   ▼
            left right
```

Use it when:

- Finding triplets/quadruplets with certain criteria
- Input is sorted
- Remaining problem reduces to Two Sum

## Templates

**Converging pointers:** start at opposite ends, move inward based on a comparison:

```plaintext
left ← 0
right ← len(arr) - 1

while left < right:
    if condition is satisfied:
        record result
        move both pointers inward
    else if condition needs more (sum too small, etc.):
        left ← left + 1
    else:
        right ← right - 1
```

**Fast–slow pointers:** both start at the same end; `slow` is the write head, `fast` is the read head:

```plaintext
slow ← 0

for fast from 0 to len(arr) - 1:
    if arr[fast] satisfies keep condition:
        arr[slow] ← arr[fast]
        slow ← slow + 1

# arr[0..slow) is the result; slow is the new length
```

**Anchored + converging:** outer loop fixes one element, inner two-pointer scan handles the rest:

```plaintext
sort arr

for i from 0 to len(arr) - 3:
    skip duplicates of arr[i]
    left ← i + 1
    right ← len(arr) - 1

    while left < right:
        if triplet found:
            record result
            skip duplicates of arr[left] and arr[right]
            left ← left + 1
            right ← right - 1
        else if sum too small:
            left ← left + 1
        else:
            right ← right - 1
```

## Recognize it when

- The clearest signal is a sorted array plus a target — sorted order lets you reason about entire sides at once ("everything to the right of `left` is larger"), which is what makes moving a single pointer valid.
- Same-direction pointers appear when the problem is about in-place rearrangement: remove duplicates, partition by condition, or filter elements. The read pointer scans every element and the write pointer advances only when an element earns its place. Because the read pointer never goes backward and the write pointer never overtakes it, the array modifies itself safely in a single pass.
- The anchored variation appears when a k-Sum problem decomposes naturally: fix one element, then the remaining target becomes a two-pointer problem on the rest. Triplets, quadruplets, and "pair closest to target" all reduce to this shape — fix all but two elements in the outer loop(s), and converge on the last pair.
- Words like *pair*, *triplet*, *target sum*, *closest*, *sorted*, and *in-place* are the surface signals. The deeper question to ask is: **does moving one pointer strictly eliminate a class of candidates that I will never need?** If yes, two pointers will work.

## Reach for something else when

- The array is unsorted and sorting would change the problem's semantics (e.g., you need the original indices). Use a hash map for two-sum instead.
- You need the nearest greater or smaller element for every index, not a pair. That is a monotonic stack.
- You need the max or min inside every window of size k. That is a monotonic deque.
- The problem involves linked-list cycle detection or finding the middle. The fast–slow pointer idea applies, but see the dedicated [Fast & Slow Pointers](../02-fast-and-slow-pointers/README.md) pattern.

## Pitfalls

- After recording a triplet or quadruplet, the outer anchor and both inner pointers must skip over equal values, otherwise the same combination appears multiple times in the output. Skipping only one pointer is the most common half-fix.
- The anchor loop in triplet problems should stop at `len - 2`, not `len - 1`, because the two inner pointers each need at least one element. Getting this wrong triggers an index error or silently skips valid pairs.
- Converging pointers only work because sorted order gives a monotone signal: if the current pair's sum is too large, every pair using the right pointer is also too large. Without sorting, moving right tells you nothing.
- When collecting multiple results (all triplets, all pairs), you need to keep moving both pointers after a match, not just once. An `if` finds the first result and stops; a `while left < right` loop finds all of them.
- In languages with fixed-width integers, summing three or four values before comparing to a target can overflow. In Python this is never an issue, but in Java or C++ you should add and compare incrementally.

## Key takeaways

- Both pointers move at most $n$ times and never go backward — that is the only reason the pattern is $O(n)$.
- Every pointer move must eliminate candidates you provably will never need. If you cannot argue why a candidate is gone forever, the move is not safe.
- Sort first when the problem permits it. Sorted order is the precondition that makes one-sided elimination valid.
- Duplicate skipping is not optional in k-Sum problems — it is required for correctness.
- Same-direction (fast–slow) and converging are distinct tools. Reach for fast–slow when the goal is in-place filtering; reach for converging when the goal is finding a pair.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
