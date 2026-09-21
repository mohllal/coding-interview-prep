# Pattern: Stacks

## Overview

A stack is a Last-In-First-Out (LIFO) data structure where elements are added and removed from the same end (the "top"). This property makes stacks ideal for problems involving nested structures, reversal, or backtracking.

## Core idea

The last element pushed is the first element popped. Think of a stack of plates: you can only add or remove from the top.

That single constraint is what makes a stack the right tool for three recurring shapes of problem:

- Matching pairs — push openers, pop when you find closers
- Reversal — push in order, pop in reverse order
- Backtracking — push state, pop to undo

Every operation is $O(1)$:

```plaintext
push(x)    → Add x to the top            O(1)
pop()      → Remove and return top       O(1)
peek()     → Return top without removing O(1)
is_empty() → Check if stack is empty     O(1)
```

### Example: matching parentheses

```plaintext
Input: "([{}])"

Step 1: '(' → push    Stack: ['(']
Step 2: '[' → push    Stack: ['(', '[']
Step 3: '{' → push    Stack: ['(', '[', '{']
Step 4: '}' → pop '{' Stack: ['(', '[']       ✓ matches
Step 5: ']' → pop '[' Stack: ['(']            ✓ matches
Step 6: ')' → pop '(' Stack: []               ✓ matches

Result: Valid (stack empty at end)
```

## Templates

Matching and nesting — push what is still open, pop when it is closed:

```python
def is_balanced(s):
    stack = []

    for ch in s:
        if ch in OPENERS:
            stack.append(ch)
        elif not stack or not matches(stack.pop(), ch):
            return False        # a closer with nothing open to match it

    return not stack            # anything left over was never closed
```

The final `not stack` check is easy to forget, and it is the half that catches `"((("`.

Monotonic stack — park indices whose answer is still unknown, and resolve them the moment the answer walks past:

```python
stack = []                      # indices waiting for an answer
result = [-1] * len(nums)

for i, num in enumerate(nums):
    while stack and nums[stack[-1]] < num:    # num answers everything smaller
        result[stack.pop()] = num
    stack.append(i)
```

Despite the nested `while`, this is $O(n)$: each index is pushed exactly once and popped at most once, so the total pop count across the whole scan is bounded by `n`.

Three knobs change the question being answered: flipping `<` to `>` finds the next *smaller* element instead, iterating right-to-left finds the *previous* one instead of the next, and `<` versus `<=` decides whether equal values resolve each other.

## Recognize it when

- The most recently seen unresolved thing is the first one that will be resolved. This is the real signal, and it is about the problem's resolution order rather than a preference for a data structure. When the *oldest* unresolved thing resolves first, you want a queue instead — that single question separates the two.
- An element cannot be answered until you reach some later element. Park it and carry on; because the nearest parked item is always the first to be satisfied, the parked items are a stack by construction. "Next greater element" is exactly this shape.
- Items already parked hold an order you would have to destroy to insert badly. If a new element invalidates some of them, popping until the order is restored keeps the stack monotonic, and the push-once-pop-once argument keeps the scan linear.
- The input nests, and the natural solution is recursive, but you want to control the memory. A stack is the call stack written out by hand — same structure, no recursion limit, and you can inspect it.
- Only the most recent state matters when undoing. Path navigation and backtracking qualify; anything needing the *best* parked item rather than the *latest* wants a heap.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
