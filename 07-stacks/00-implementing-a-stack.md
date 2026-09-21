---
title: Implementing a Stack
difficulty: Easy
tags:
  - Stack
---

# Implementing a Stack

## Problem description

Implement a stack data structure with the following operations:

- `push(val)`: Add an element to the top
- `pop()`: Remove and return the top element
- `peek()`: Return the top element without removing it
- `size()`: Return the number of elements
- `is_empty()`: Check if the stack is empty

## Hints

<details>
<summary>Hint 1</summary>

Every operation has to be $O(1)$, which rules out anything that shifts elements. Which end of an array or list can you add to and remove from cheaply?

</details>

<details>
<summary>Hint 2</summary>

An array appending and popping at the back, or a linked list inserting and removing at the front. Both give last-in-first-out in constant time.

</details>

## Solution 1: Array-based implementation

### Intuition

Use a fixed-size array with a `top` pointer tracking the index of the last valid element. Start at `-1` (empty), increment on push, decrement on pop.

### Complexity analysis

- Time complexity: $O(1)$ for all operations
- Space complexity: $O(n)$ where n is the capacity of the stack

```python
class Stack:
    def __init__(self, capacity: int):
        if capacity <= 0:
            raise ValueError("Capacity must be positive")

        self.capacity = capacity
        self.arr = [None] * capacity
        self.top = -1

    def push(self, val):
        if self.is_full():
            raise OverflowError("Stack overflow")

        self.top += 1
        self.arr[self.top] = val

    def pop(self):
        if self.is_empty():
            raise IndexError("Stack underflow")

        value = self.arr[self.top]
        self.arr[self.top] = None
        self.top -= 1

        return value

    def peek(self):
        if self.is_empty():
            raise IndexError("Stack is empty")

        return self.arr[self.top]

    def size(self):
        return self.top + 1

    def is_empty(self):
        return self.top == -1

    def is_full(self):
        return self.top == self.capacity - 1
```

## Solution 2: Linked list implementation

### Intuition

Use a singly linked list where the head is the top of the stack. Push prepends a new node, pop removes the head. No fixed capacity.

### Complexity analysis

- Time complexity: $O(1)$ for all operations
- Space complexity: $O(n)$ for n elements

```python
class Node:
    def __init__(self, val, next=None):
        self.val = val
        self.next = next

class Stack:
    def __init__(self):
        self.head = None
        self.size = 0

    def push(self, val):
        self.head = Node(val, self.head)
        self.size += 1

    def pop(self):
        if self.is_empty():
            raise IndexError("Stack underflow")
  
        value = self.head.val
        self.head = self.head.next
        self.size -= 1
        return value

    def peek(self):
        if self.is_empty():
            raise IndexError("Stack is empty")

        return self.head.val

    def size(self):
        return self.size

    def is_empty(self):
        return self.head is None
```
