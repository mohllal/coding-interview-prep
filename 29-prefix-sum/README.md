# Pattern: Prefix Sum

## Overview

The Prefix Sum pattern computes cumulative sums of an array up front so that the sum of any subarray can be answered in O(1) rather than O(n).

Without preprocessing, summing the elements between two indices means looping through them every time — O(n) per query. With a prefix array, that becomes a single subtraction, turning O(n) per query into O(1) after an O(n) build step.

## Example

Given `nums = [3, 1, 4, 1, 5, 9, 2, 6]`, answer several "what is the sum from index i to j?" queries without re-scanning.

Build the prefix array, where `prefix[i]` = sum of the first `i` elements (so `prefix[0] = 0`):

```plaintext
index:   0   1   2   3   4   5   6   7   8
nums:    3   1   4   1   5   9   2   6
prefix:  0   3   4   8   9  14  23  25  31
```

Now any range sum is an O(1) lookup:

```plaintext
sum(2, 5)  =  prefix[6] - prefix[2]  =  23 - 4  =  19   ✓  (4 + 1 + 5 + 9)
sum(0, 3)  =  prefix[4] - prefix[0]  =   9 - 0  =   9   ✓  (3 + 1 + 4 + 1)
sum(6, 7)  =  prefix[8] - prefix[6]  =  31 - 23 =   8   ✓  (2 + 6)
```

The formula: `sum(i, j)` = `prefix[j + 1] - prefix[i]`.

## Core idea

Define `prefix[i]` = sum of `nums[0..i-1]`, with `prefix[0] = 0`. Then:

```plaintext
sum(i, j) = prefix[j + 1] - prefix[i]
```

The way to hold this in your head: `prefix` does not index the elements, it indexes the gaps between them. An array of `n` elements has `n + 1` gaps — one before the first element, one after the last, and one between each neighbouring pair. `prefix[i]` is the running total as you stand at gap `i`.

```plaintext
gap:      0       1       2       3       4
          ┆   3   ┆   1   ┆   4   ┆   1   ┆
prefix:   0       3       4       8       9
```

Gap 0 sits before anything has been added, so `prefix[0] = 0` is the odometer reading before you start driving, not a special case bolted onto the front.

Reading a range off this picture is then mechanical. `sum(i, j)` is the stretch from the gap on the left of element `i` to the gap on the right of element `j` — gap `i` and gap `j + 1`. That is where the `+ 1` comes from: not an off-by-one to memorize, just which fencepost you are standing on.

The key move for subarray-sum problems: at each index `j`, you hold `prefix[j]`. You want some earlier index `i` where `prefix[j] - prefix[i] = k`, i.e., `prefix[i] = prefix[j] - k`. Store every prefix value seen so far in a hash map. One lookup answers the question in O(1).

The same gap-0 idea explains why that map is seeded with `{0: 1}`: gap 0 is a real boundary a subarray can start from, so the running total of `0` must already be in the map before the loop begins.

This works even with negative numbers, where a sliding window cannot be used because shrinking the window does not reliably decrease the sum.

## Variations

### 1. Range sum queries

The direct application: build the prefix array once, answer `sum(i, j)` queries in O(1) each. This is the foundation every other variant builds on.

### 2. Find or count subarrays with exact sum

Maintain a running prefix sum and a hash map. At each index `j`, look up `prefix[j] - k` in the map. For a count of subarrays, store frequencies; for existence, store a boolean.

Seed the map with `{0: 1}` before the loop — otherwise subarrays starting at index 0 are missed.

### 3. Find the longest or shortest valid subarray

When you want length rather than count, the map stores index positions: the first occurrence of each prefix sum for the longest subarray, or the last occurrence for the shortest.

### 4. Subarrays divisible by k

Two prefix sums with the same remainder mod `k` indicate a subarray whose sum is divisible by `k`. Count how many times each remainder has appeared. Seed with remainder `0 → 1`.

### 5. Pivot index / balance point

Find an index where the left sum equals the right sum. At each index `i`, left sum is the running total before `i`, and right sum is `total - running_total - nums[i]`. No extra array needed.

## Templates

There are only two shapes. Everything else is a choice of what you put in the map.

**Materialize the prefix array** when ranges are queried in arbitrary order:

```python
prefix = [0] * (len(nums) + 1)
for i, num in enumerate(nums):
    prefix[i + 1] = prefix[i] + num

range_sum = prefix[j + 1] - prefix[i]     # sum of nums[i..j], inclusive
```

If you only need left and right context while scanning forward, the array is unnecessary — a running sum plus the total gives both sides in O(1) space:

```python
total = sum(nums)
left = 0

for i, num in enumerate(nums):
    right = total - left - num
    ...
    left += num
```

**Scan once with a map of prefixes already seen** when the answer is about a pair of boundaries:

```python
from collections import defaultdict

seen = defaultdict(int)
seen[0] = 1          # the empty prefix, sitting before index 0
prefix = 0
result = 0

for num in nums:
    prefix += num
    result += seen[prefix - k]    # earlier boundaries that close a range here
    seen[prefix] += 1
```

Three things change from problem to problem: the **key** you store, the **value** you store against it, and the **partner** you look up.

- **Count subarrays summing to `k`**:  
  - Key: `prefix`  
  - Value: frequency  
  - Look up: `prefix - k`

- **Longest subarray summing to `k`**:  
  - Key: `prefix`  
  - Value: first index seen  
  - Look up: `prefix - k`

- **Shortest subarray summing to `k`**:  
  - Key: `prefix`  
  - Value: last index seen  
  - Look up: `prefix - k`

- **Count subarrays divisible by `k`**:  
  - Key: `prefix % k`  
  - Value: frequency  
  - Look up: `prefix % k`

- **Longest run with equal 0s and 1s**:  
  - Key: running ±1 score  
  - Value: first index seen  
  - Look up: the same score

Which value to store follows from what you are measuring:
    - Frequencies count subarray
    - The earliest index maximizes `j - seen[partner]`, so it gives the longest
    - The latest index minimizes it, so it gives the shortest.

## Recognize it when

- The quantity you want over a range is the difference of two cumulative values. Formally `f(i, j) = F(j) - F(i)` for some "F up to here", which requires the underlying operation to be invertible: addition undone by subtraction, XOR undone by XOR, a running count undone by subtraction. Sums are the common case, not the only one.
- The answer depends on a pair of boundaries but you can only scan one of them at a time. Remembering every left boundary you have passed lets the current right boundary find its partner in a single lookup, which is what collapses an $O(n^2)$ pair search into one pass.
- Your brute force has an inner loop that does nothing but accumulate. If the inner body is `running += nums[j]` and nothing more, every outer iteration is recomputing what the previous one already knew.
- Each element needs context from both sides at once. "Everything before `i`" is a running sum and "everything after `i`" is `total - running - nums[i]`, so both are available without a second pass.
- A sliding window nearly fits, but the window summary is not monotone. Negatives break the shrink rule, since growing the window can decrease the sum and shrinking then proves nothing. Prefix sums compare boundaries directly and never need that invariant.
- The real question is whether two positions fall into the same equivalence class. "Sum divisible by `k`" means two prefixes share a remainder; "equal numbers of 0s and 1s" means they share a running ±1 score; "every letter appears an even number of times" means they share a parity bitmask. Whenever the question reduces to "have I been in this state before", the map key is a derived prefix and the pattern still applies.

## Reach for something else when

- The array has only non-negative integers and you want the shortest subarray with sum ≥ k: [Sliding Window](../03-sliding-window/README.md) shrinks cleanly and uses O(1) space.
- The ranges are intervals to be merged or compared rather than summed: [Merge Intervals](../04-merge-intervals/README.md).
- Values are updated between queries: a Fenwick Tree or Segment Tree handles dynamic updates in O(log n).
- You need the maximum or minimum inside every window of size k: a monotonic deque maintains that in O(1) amortized per step.

## Pitfalls

- Forgetting the zero sentinel. Without `prefix[0] = 0` (and seeding the map with `{0: 1}`), every subarray that begins at index 0 is silently missed.
- Off-by-one in the formula. `sum(i, j)` = `prefix[j+1] - prefix[i]`, where `j` is inclusive. A one-off produces wrong answers with no error.
- Storing the last occurrence instead of the first when searching for the longest subarray (or vice versa for shortest).
- Negative modular arithmetic. Python's `%` always returns non-negative, so `(-1) % 3 == 2`. In other languages, normalize with `((r % k) + k) % k`.
- Reaching for a sliding window on an array with negatives. The window sum is not decreasing with negatives — use prefix sums instead.

## Key takeaways

- `prefix[i]` = sum of the first `i` elements *excluding* index `i` (i.e., sum of `nums[0]` through `nums[i-1]`), with `prefix[0] = 0`. This lets you compute the sum of any subarray `[i, j]` as `prefix[j+1] - prefix[i]`.
- For subarray-count problems, store prefix sum frequencies. Seed with `{0: 1}` before the loop.
- For longest-subarray problems, store the first occurrence of each prefix sum. For shortest, store the last.
- For divisibility problems, reduce prefix sums modulo `k` and count matching remainders.
- When negatives are present, sliding window breaks; prefix sums with a hash map take over.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
