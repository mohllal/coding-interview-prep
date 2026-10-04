# Pattern: Fast & Slow Pointers

## Overview

The Fast & Slow Pointers pattern (also known as Floyd's tortoise and hare) walks two pointers through the same sequence at different speeds: `slow` takes one step at a time, `fast` takes two.

The fixed **ratio** between their speeds turns into two useful facts.

- If the sequence loops, `fast` must eventually land on `slow`, which detects the cycle without remembering anything you've seen.
- If the sequence ends, `fast` reaches the end exactly when `slow` is halfway, which finds the middle without knowing the length.

Both answers come in a single pass with $O(1)$ extra space.

The sequence doesn't need to be a linked list. Anything where each state determines the next one — `next = step(current)` — can be walked this way.

> Core idea: two pointers moving at speeds 1 and 2 through a sequence. Inside a cycle, the gap between them shrinks by one every step, so they must meet while on a path with an end, fast finishes when slow is at the middle.

## The core pattern

Picture both pointers inside a cycle, like runners on a circular track. Every iteration `fast` moves 2 and `slow` moves 1, so `fast` gains exactly one step and the gap between them shrinks by 1. However far apart they start, the gap eventually reaches 1 or 2:

- If `fast` is 1 step behind, the next iteration moves it 2 and `slow` 1, and they land on the same node.
- If `fast` is 2 steps behind, the next iteration leaves it 1 step behind, which is the first case.

A gap that shrinks by exactly 1 can't jump over 0, so they cannot pass each other without meeting. A speed-3 pointer gains 2 per step and could leap over `slow`; that's why the ratio is 2.

**Without a cycle**, `fast` falls off the end:

```plaintext
A ──▶ B ──▶ C ──▶ D ──▶ E ──▶ F ──▶ G ──▶ null

start         slow = A   fast = A
iteration 1   slow = B   fast = C
iteration 2   slow = C   fast = E
iteration 3   slow = D   fast = G
iteration 4   fast.next is null → stop: no cycle, slow = D is the middle
```

**With a cycle**, `fast` laps `slow` and lands on it:

```plaintext
A ──▶ B ──▶ C ──▶ D ──▶ E ──▶ F
                  ▲           │
                  └───────────┘

start         slow = A   fast = A
iteration 1   slow = B   fast = C
iteration 2   slow = C   fast = E
iteration 3   slow = D   fast = D   → meet: cycle detected
```

## Templates

**Walk until fast ends or the pointers meet**: one loop answers both "is there a cycle?" and "where is the middle?", depending on how it exits:

```plaintext
slow ← head
fast ← head
while fast is not null and fast.next is not null:
    slow ← slow.next
    fast ← fast.next.next
    if slow is fast:
        return "cycle", slow            # slow is the meeting point
return "no cycle", slow                 # slow is the middle node
```

**Find where the cycle starts**: after the pointers meet, move one back to the head and advance both one step at a time:

```plaintext
finder ← head
while finder is not slow:
    finder ← finder.next
    slow ← slow.next
return finder                           # first node of the cycle
```

**Any step function**: replace `.next` with any deterministic `step(state)` that has a finite number of possible states:

```plaintext
slow ← start
fast ← start
repeat:
    slow ← step(slow)
    fast ← step(step(fast))
until slow = fast
```

Three things change from problem to problem: what **step** means (`.next`, a digit-square sum, an array jump), what you do **once the pointers meet** (stop, measure the cycle, find its start), and what you do **when fast reaches the end** (report no cycle, or use `slow` as the middle).

## Variations

### 1. Detect a cycle

Run the first template and report whether the pointers met ([Linked List Cycle](./01-linked-list-cycle.md)). This is the $O(1)$-space replacement for "store every visited node in a set".

### 2. Measure the cycle

Once the pointers meet, both are inside the cycle. Hold one still and walk the other around until it returns, counting steps ([Linked List Cycle Length](./01.1-linked-list-cycle-length.md)).

### 3. Find where the cycle starts

The second template finds the entrance. Why it works: let `a` be the distance from the head to the cycle's start, `b` the distance from the start to the meeting point, and `c` the cycle length.

```plaintext
head ──── a ────▶ start ──── b ────▶ meet
                    ▲                  │
                    └──── c - b ───────┘
```

When they meet, `slow` has walked `a + b`, and `fast` has walked the same plus some whole number of laps: `a + b + k·c`. Since `fast` walks twice as far as `slow`:

```plaintext
2(a + b) = a + b + k·c
    a + b = k·c
        a = k·c - b = (c - b) + (k - 1)·c
```

So walking `a` steps from the meeting point covers the remaining `c - b` to the start, plus whole laps that end back at the start. A pointer walking `a` steps from the head reaches the start at the same moment, and that's where the two meet ([Start of Linked List Cycle](./03-start-of-linked-list-cycle.md)).

### 4. Find the middle

Run the first template on a list without a cycle; when `fast` can't take two more steps, `slow` is at the middle ([Middle of the Linked List](./02-middle-of-the-linked-list.md)). The loop condition decides which middle you get for an even length:

```plaintext
1 → 2 → 3 → 4

while fast and fast.next             → slow stops on 3 (second middle)
while fast.next and fast.next.next   → slow stops on 2 (first middle)
```

### 5. Middle, then reverse the second half

Splitting a list into halves is the first step of several list problems. Find the middle, reverse the second half in place, then walk both halves together. That's how to compare ends without extra space ([Palindrome Linked List](./05-palindrome-linked-list.md)) or interleave them ([Rearrange a Linked List](./06-rearrange-a-linked-list.md)).

### 6. Sequences without a linked list

Any deterministic step with finitely many states must eventually repeat, so it forms an implicit linked list. Repeatedly summing the squares of digits either reaches 1 or loops ([Happy Number](./04-happy-number.md)). Treating each value as a pointer to an index, `i → nums[i]`, turns an array with a duplicate into a list whose cycle starts at the duplicate ([Find the Duplicate Number](./03.1-find-the-duplicate-number.md)). Jumping `i → (i + nums[i]) % n` around an array forms cycles too, with extra rules about which ones count ([Cycle in a Circular Array](./07-cycle-in-a-circular-array.md)).

## Recognize it when

- Each state or position in the sequence has exactly one next state (deterministic transition).
- The main question is whether following the sequence eventually repeats (cycle detection), where the cycle starts, or how long the cycle lasts.
- The sequence could be:
  - A linked list,
  - A number repeatedly transformed (e.g., sum of digits squared),
  - An array where values point to indices.
- The structure might loop forever and cannot be fully measured in advance (so strategies like "find the length, then..." won't apply).
- You need a result defined relative to the end (like "middle" or "start of the second half"), but can only traverse forward from the beginning.
- The problem could be solved by tracking visited positions, but you're required to use $O(1)$ space (so tracking with a set is not allowed). Using two pointers at different speeds is the classic alternative.

## Reach for something else when

- Memory isn't constrained and you need more than one answer: for example, every node that sits in a cycle. A hash set of visited states is simpler and just as fast.
- The two positions are a fixed gap apart, not a fixed ratio: `k`-th from the end, or removing the `n`-th node from the end. Start one pointer `k` steps ahead and move both at the same speed.
- You have random access: the middle of an array is just index `n // 2`.
- A state can lead to more than one next state: Fast & slow needs exactly one successor per state. This is general graph and we can use DFS with visited markers for cycle detection there.
- The array is sorted and you're looking for pairs: converging two pointers fits better.

## Pitfalls

- Checking `fast.next` before `fast`: write `fast and fast.next` in that order, or a `None` crashes the loop.
- Comparing before moving: both pointers start on the head, so a check at the top of the loop reports a cycle immediately. Move first, then compare.
- Comparing values instead of nodes: two different nodes can hold the same value. Compare node identity with `is`, not `==`.
- Getting the wrong middle for even lengths: decide whether you need the first or second middle and pick the loop condition to match. When splitting into halves, also know which half gets the extra node on odd lengths.
- Leaving the list modified: reversing the second half changes the caller's list. Reverse it back before returning if the input must be preserved.
- Counting a trivial loop as a cycle: in circular-array problems, a single element jumping to itself, or a loop that changes direction, may not count. Check those rules on every step.

## Key takeaways

- Speeds 1 and 2 shrink the gap inside a cycle by exactly one per step, so the pointers can't skip past each other.
- One loop gives either a meeting point (there's a cycle) or the middle (there isn't).
- To find the cycle's entrance, restart one pointer at the head and move both one step at a time; they meet at the entrance because `a = (c - b) + (k - 1)c`.
- Any deterministic step function over finitely many states is an implicit linked list.
- The second pointer replaces a visited set, turning $O(n)$ space into $O(1)$.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
