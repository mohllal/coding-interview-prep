# Top K Elements: Problems

## If I'm in a hurry

| #  | Problem                                                                  | Why this one                                                                           | Difficulty |
|----|--------------------------------------------------------------------------|----------------------------------------------------------------------------------------|------------|
| 01 | [Kth Smallest Number](./01-kth-smallest-number.md)                       | Max-heap, heapify, and QuickSelect — three distinct solutions for the same problem     | Medium     |
| 02 | [Top K Numbers](./02-top-k-numbers.md)                                   | Baseline: min-heap of size K for K largest                                             | Easy       |
| 04 | [Connect Ropes](./04-connect-ropes.md)                                   | Greedy + heap: always merge the two cheapest (Huffman structure)                       | Medium     |
| 05 | [Top K Frequent Numbers](./05-top-k-frequent-numbers.md)                 | Heap keyed by frequency, not value                                                     | Medium     |
| 07 | [Kth Largest Number in a Stream](./07-kth-largest-number-in-a-stream.md) | Persistent min-heap of size K; root is always the answer                               | Easy       |
| 08 | [K Closest Numbers](./08-k-closest-numbers.md)                           | Two approaches: heap with custom tie-breaking key, and binary search on a sorted array | Medium     |

## All problems

Problems marked *(unsolved)* have the statement but no solution yet.

| #  | Problem                                                                                     | Variant                                             | Difficulty |
|----|---------------------------------------------------------------------------------------------|-----------------------------------------------------|------------|
| 01 | [Top K Numbers](./01-top-k-numbers.md)                                                      | Min-heap of size K                                  | Easy       |
| 02 | [Kth Smallest Number](./02-kth-smallest-number.md)                                          | Max-heap of size K, heapify, or QuickSelect         | Medium     |
| 03 | [K Closest Points to the Origin](./03-k-closest-points-to-origin.md)                        | Max-heap keyed by squared distance                  | Medium     |
| 04 | [Connect Ropes](./04-connect-ropes.md)                                                      | Greedy min-heap (Huffman merge)                     | Medium     |
| 05 | [Top K Frequent Numbers](./05-top-k-frequent-numbers.md)                                    | Min-heap keyed by frequency                         | Medium     |
| 06 | [Frequency Sort](./06-frequency-sort.md)                                                    | Max-heap; rebuild string from frequency order       | Medium     |
| 07 | [Kth Largest Number in a Stream](./07-kth-largest-number-in-a-stream.md)                    | Persistent min-heap of size K                       | Easy       |
| 08 | [K Closest Numbers](./08-k-closest-numbers.md) *(unsolved)*                                 | Max-heap with `(-dist, -val)` key, or binary search | Medium     |
| 09 | [Maximum Distinct Elements](./09-maximum-distinct-elements.md) *(unsolved)*                 | Greedy: remove cheapest duplicates first            | Medium     |
| 10 | [Sum of Elements](./10-sum-of-elements.md) *(unsolved)*                                     | Heapify then pop K1 through K2-1 times              | Medium     |
| 11 | [Rearrange String](./11-rearrange-string.md) *(unsolved)*                                   | Max-heap + cooldown buffer                          | Medium     |
| 12 | [Rearrange String K Distance Apart](./12-rearrange-string-k-distance-apart.md) *(unsolved)* | Max-heap + queue-based cooldown of length K         | Hard       |
| 13 | [Scheduling Tasks](./13-scheduling-tasks.md) *(unsolved)*                                   | Max-heap simulation or closed-form formula          | Medium     |
| 14 | [Frequency Stack](./14-frequency-stack.md) *(unsolved)*                                     | Frequency map + per-frequency stacks                | Hard       |
