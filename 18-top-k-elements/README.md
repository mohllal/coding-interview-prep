# Pattern: Top K Elements

## Overview

Top K problems ask for the `k` largest, smallest, most frequent or closest items in a collection — or for the single `k`-th one. Sorting everything answers them in $O(n \log n)$, but most of that work orders elements you will never look at.

A heap of size `k` keeps only the candidates that still matter, which cuts the cost to $O(n \log k)$ — a big win when `k` is much smaller than `n`.

> Core idea: keep a heap of the `k` best candidates seen so far, with the *worst* of them at the root, so each new element only has to beat the root to get in.

## Core technique

The heap is the **opposite** of what you're looking for, which is the part that sometimes becomes confusing:

- For the `k` **smallest**, use a **max-heap**. Its root is the largest of the `k` smallest — the first one to evict when something smaller arrives.
- For the `k` **largest**, use a **min-heap**. Its root is the smallest of the `k` largest.

Either way, once the scan finishes the heap holds the top `k`, and its root is the `k`-th one.

```plaintext
k smallest of [1, 5, 12, 2, 11, 5], k = 3   (max-heap)

fill     1, 5, 12           heap = {12, 5, 1}   root 12
2  < 12  evict 12, add 2    heap = {5, 2, 1}    root 5
11 > 5   skip
5  = 5   skip

→ the 3 smallest are {1, 2, 5}. The 3rd smallest is the root, 5
```

## Template

```plaintext
heap ← empty heap ordered so the WORST candidate is at the root
for each of the first k elements x:
    push x

for each remaining element x:
    if x is better than root:
        pop root
        push x

return heap            # the top k
# or: return root      # the k-th
```

Three things change from problem to problem: what **"better"** means (smaller, larger, more frequent, closer), what goes into the heap (the value, or a `(key, value)` pair), and whether you return the whole heap or just its root.

## Recognize it when

The problem says "top k", "k largest", "k smallest", "k most frequent", "k closest" or "the k-th". The input is unsorted, and `k` is usually much smaller than `n`. You need the best few items without caring how the rest are ordered.

## Reach for something else when

- **You need only the k-th element and $O(n)$ average time matters:** QuickSelect partitions in place without a heap, at the cost of an $O(n^2)$ worst case.
- **You need the median, or a value in the middle of a changing set:** that's [Two Heaps](../14-two-heaps/README.md).
- **The input is already sorted, or is several sorted lists:** read directly from the ends, or use a K-way merge.
- `k` **is close to** `n`**:** just sort. A heap of size `k` no longer saves anything.

## Pitfalls

- Using the heap that matches the question instead of the opposite one. The `k` smallest need a max-heap.
- Forgetting that Python's `heapq` is a min-heap only. Negate values to simulate a max-heap, and negate again when reading.
- Letting the heap grow to `n`. Pop as soon as its size passes `k`, or the complexity drops back to $O(n \log n)$.
- Comparing with `<=` instead of `<` when deciding to replace the root. Equal values don't need to swap in.

## Key takeaways

- A heap of size `k` turns $O(n \log n)$ sorting into $O(n \log k)$.
- Keep the worst of the current candidates at the root. That's what each new element competes with.
- `k` smallest → max-heap .. `k` largest → min-heap.
- After the scan, the heap is the top `k` and its root is the `k`-th.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list.
