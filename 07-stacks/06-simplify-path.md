---
title: Simplify Path
difficulty: Medium
leetcode: https://leetcode.com/problems/simplify-path/
tags:
  - String
  - Stack
---

# Simplify Path

## Problem description

Given an absolute path for a Unix-style file system, which begins with a slash `'/'`, transform this path into its **simplified canonical path**.

The rules are:

- A single period `'.'` represents the current directory
- A double period `'..'` represents the parent directory
- Multiple consecutive slashes `'//'` are treated as a single slash
- Any other format of periods (e.g., `'...'`) is treated as a directory name

The canonical path should:

- Start with a single slash `'/'`
- Directories separated by a single slash `'/'`
- Not end with a trailing slash (unless it's the root)
- Not have any `.` or `..` components

## Examples

**Example 1:**

```plaintext
Input: path = "/home/"
Output: "/home"
```

**Example 2:**

```plaintext
Input: path = "/home//foo/"
Output: "/home/foo"
```

**Example 3:**

```plaintext
Input: path = "/home/user/Documents/../Pictures"
Output: "/home/user/Pictures"
```

**Example 4:**

```plaintext
Input: path = "/../"
Output: "/"
Explanation: Going up from root stays at root
```

## Constraints

- `1 <= path.length <= 3000`
- `path` consists of English letters, digits, period `'.'`, slash `'/'`, or underscore `'_'`
- `path` is a valid absolute Unix path

## Hints

<details>
<summary>Hint 1</summary>

Split on `/` and most of the work becomes deciding what to do with each component. Three of them are special: empty, `.` and `..`.

</details>

<details>
<summary>Hint 2</summary>

`..` has to undo the previous component — the most recently added one. That is a pop, which makes a stack the natural structure.

</details>

## Solution

### Intuition

A stack naturally handles the parent directory (`..`) operation: push directories, pop on `..`. Split the path by `/`, process each component, and join the final stack.

### Algorithm

1. Split path by `'/'` and filter out empty strings and `'.'`
2. For each remaining component:
   - If `'..'`: pop from stack (if not empty)
   - Otherwise: push directory name onto stack
3. Join stack with `'/'` and prepend `'/'`

### Complexity analysis

- Time complexity: $O(n)$ — process each character once
- Space complexity: $O(n)$ — stack may hold all directory names

```python
class Solution:
    def simplify_path(self, path: str) -> str:
        # Split the path by '/' and remove:
        # - empty parts (caused by '//' or leading '/')
        # - '.' since it represents the current directory
        components = [part for part in path.split("/") if part and part != "."]

        stack = []
        for part in components:
            if part == ".." and stack:
                # ".." means go up one directory
                # Only pop if there is a directory to go back from
                if stack:
                    stack.pop()
            else:
                # Valid directory name, push onto the stack
                stack.append(part)

        # Rebuild the canonical path from the stack.
        # Prepend '/' to ensure an absolute path.
        #
        # This will not produce '//' duplicates because:
        # - If the stack is empty, returns just '/'
        # - If the stack has elements, '/'.join(stack) does not start with '/', so single '/' is added.
   
        return "/" + "/".join(stack)
```
