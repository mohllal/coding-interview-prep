# Heap

A heap is a **complete binary tree** (every level is fully filled except possibly the last, which fills left to right) that satisfies the **heap property**:

- **Max-heap:** every node is ≥ all of its descendants. The root holds the maximum.
- **Min-heap:** every node is ≤ all of its descendants. The root holds the minimum.

> The heap property only constrains a node relative to its **own** subtree, not relative to nodes in other subtrees. A node's left child may be larger or smaller than its right child — that's fine.

## Array representation

A heap is stored as a flat array, not with pointers. For a node at index `i`:

```plaintext
parent      →  (i - 1) // 2
left child  →  2 * i + 1
right child →  2 * i + 2
```

So this max-heap tree:

```plaintext
              10            index 0
            /    \
           7      9         index 1, 2
          / \    /
         3   5  8           index 3, 4, 5
```

is stored as:

```plaintext
index:  0   1   2   3   4   5
array: [10,  7,  9,  3,  5,  8]

node 1 (value 7):  parent = (1-1)//2 = 0  → 10 ✓
                   left   = 2*1+1    = 3  →  3
                   right  = 2*1+2    = 4  →  5

node 2 (value 9):  parent = (2-1)//2 = 0  → 10 ✓
                   left   = 2*2+1    = 5  →  8
                   right  = 2*2+2    = 6  →  (empty)
```

## Operations

Two internal operations restore the heap property after every change:

### Bubble up — used after `push`

Insert the new element at the end of the array (next available slot in the tree), then walk it upward, swapping with its parent until the parent is larger (max-heap) or until it reaches the root.

```plaintext
push(11) into [10, 7, 9, 3, 5, 8]

Step 1: append 11 at index 6

              10
            /    \
           7      9
          / \    / \
         3   5  8  11   ← 11 > parent 9, violates max-heap

Step 2: swap 11 ↔ 9  (index 6 ↔ 2)

              10
            /    \
           7      11   ← 11 > parent 10, violates max-heap
          / \    / \
         3   5  8   9

Step 3: swap 11 ↔ 10  (index 2 ↔ 0)

              11         ← root, stop
            /    \
           7      10
          / \    / \
         3   5  8   9  ✓
```

### Bubble down — used after `pop`

The root (the maximum) is what we return. To fill the gap, move the **last** element to the root, then walk it downward, swapping with the **larger** child (max-heap) until both children are smaller or it reaches a leaf.

```plaintext
pop() from [11, 7, 10, 3, 5, 8, 9]  → returns 11

Step 1: swap root ↔ last, remove last

               9           ← 9 just moved from leaf to root
            /    \
           7      10
          / \    /
         3   5  8

Step 2: 9 vs children 7 and 10 — larger child is 10, swap  (index 0 ↔ 2)

              10
            /    \
           7       9   ← 9 vs child 8 — 9 > 8, stop
          / \    /
         3   5  8     ✓
```

## Complexity

| Operation  | Time        | Why                                         |
|------------|-------------|---------------------------------------------|
| `push`     | $O(\log n)$ | Bubble up travels at most the tree height   |
| `pop`      | $O(\log n)$ | Bubble down travels at most the tree height |
| `peek`     | $O(1)$      | Root is always `heap[0]`                    |
| `heapify`  | $O(n)$      | One-pass build, not $n$ individual pushes   |

## Max-heap implementation

```python
class MaxHeap:
    def __init__(self):
        self.heap = []

    def push(self, value):
        self.heap.append(value)
        self._bubble_up(len(self.heap) - 1)

    def pop(self):
        if not self.heap:
            return None
        self._swap(0, len(self.heap) - 1)
        value = self.heap.pop()
        self._bubble_down(0)
        return value

    def peek(self):
        return self.heap[0] if self.heap else None

    def _bubble_up(self, i):
        # Bubble up: move value at index i up the heap until the max-heap property is restored
        while i > 0:
            parent = (i - 1) // 2
            if self.heap[i] > self.heap[parent]:
                self._swap(i, parent)
                i = parent
            else:
                break
           

    def _bubble_down(self, i):
        # Bubble down: move value at index i down the heap until the max-heap property is restored
        n = len(self.heap)
        while True:
            largest = i
            left, right = 2 * i + 1, 2 * i + 2

            # Check if left child exists and is greater than current largest
            if left < n and self.heap[left] > self.heap[largest]:
                largest = left

            # Now check if right child exists and is greater than the (possibly updated) largest
            if right < n and self.heap[right] > self.heap[largest]:
                largest = right

            # If no child is larger than the current node, we're done bubbling down
            if largest == i:
                break

            self._swap(i, largest)
            i = largest
       

    def _swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]
```

## Min-heap implementation

Identical to max-heap except comparisons flip: bubble up swaps when the child is **smaller** than its parent, and bubble down swaps with the **smaller** child.

```python
class MinHeap:
    def __init__(self):
        self.heap = []

    def push(self, value):
        self.heap.append(value)
        self._bubble_up(len(self.heap) - 1)

    def pop(self):
        if not self.heap:
            return None
        self._swap(0, len(self.heap) - 1)
        value = self.heap.pop()
        self._bubble_down(0)
        return value

    def peek(self):
        return self.heap[0] if self.heap else None

    def _bubble_up(self, i):
        while i > 0:
            parent = (i - 1) // 2
            if self.heap[i] < self.heap[parent]:
                self._swap(i, parent)
                i = parent
            else:
                break

    def _bubble_down(self, i):
        n = len(self.heap)
        while True:
            smallest = i
            left, right = 2 * i + 1, 2 * i + 2
            if left < n and self.heap[left] < self.heap[smallest]:
                smallest = left
            if right < n and self.heap[right] < self.heap[smallest]:
                smallest = right
            if smallest == i:
                break
            self._swap(i, smallest)
            i = smallest

    def _swap(self, i, j):
        self.heap[i], self.heap[j] = self.heap[j], self.heap[i]
```

## Python's `heapq`

Python only ships a min-heap. To get a max-heap, negate values on the way in and out:

```python
import heapq

min_heap = []
heapq.heappush(min_heap, 5)   # push 5
heapq.heappop(min_heap)       # pop and return smallest

max_heap = []
heapq.heappush(max_heap, -5)  # push 5 (negated)
-heapq.heappop(max_heap)      # pop and return largest (un-negate)
```
