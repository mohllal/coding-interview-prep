---
title: Maximum Number of Balloons
difficulty: Easy
leetcode_title: Maximum Number of Balloons
leetcode: https://leetcode.com/problems/maximum-number-of-balloons/
tags:
  - Hash Table
  - String
  - Counting
---

# Maximum Number of Balloons

## Problem description

Given a string, determine the maximum number of times the word `"balloon"` can be formed using the characters from the string. Each character in the string can be used only once.

## Examples

**Example 1:**

```plaintext
Input:  "balloonballoon"
Output: 2

"balloon" can be formed twice.
```

**Example 2:**

```plaintext
Input:  "bbaall"
Output: 0

We need 'o' twice but "bbaall" has none.
```

**Example 3:**

```plaintext
Input:  "balloonballoooon"
Output: 2

"balloon" can still only be formed twice — extra 'o' characters don't help.
```

## Constraints

- `1 <= text.length <= 10^4`
- `text` consists of lowercase English letters only.

## Hints

<details>
<summary>Hint 1</summary>

"balloon" requires specific characters: b, a, l, l, o, o, n. Some of them are needed more than once — count how many of each the string provides.

</details>

<details>
<summary>Hint 2</summary>

For each distinct character in "balloon", the number of times it can fulfill that role is `available_count // required_count`. The bottleneck character determines the answer.

</details>

## Solution

### Intuition

"balloon" uses: `b×1, a×1, l×2, o×2, n×1`. For each required character, compute how many full contributions the source string can provide (floor division by required count). The minimum across all characters is the answer — the scarcest resource is the bottleneck.

```plaintext
text = "balloonballoon"

Source counts:        Required per "balloon":
  b → 2                 b → 1   →  2 // 1 = 2
  a → 2                 a → 1   →  2 // 1 = 2
  l → 4                 l → 2   →  4 // 2 = 2
  o → 4                 o → 2   →  4 // 2 = 2
  n → 2                 n → 1   →  2 // 1 = 2

min(2, 2, 2, 2, 2) = 2
```

### Algorithm

1. Build a frequency map of `text`
2. Build a frequency map of `"balloon"`
3. For each character in `"balloon"`, compute `text_count[c] // balloon_count[c]`
4. Return the minimum

### Complexity analysis

- Time complexity: $O(n)$ — one pass to count, one constant-size pass over "balloon"'s characters
- Space complexity: $O(1)$ — both frequency maps are bounded by alphabet size

```python
from collections import Counter


class Solution:
    def maxNumberOfBalloons(self, text: str) -> int:
        text_freq = Counter(text)
        balloon_freq = Counter("balloon")

        return min(text_freq[c] // needed for c, needed in balloon_freq.items())
```
