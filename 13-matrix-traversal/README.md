# Pattern: Matrix Traversal

## Overview

The Matrix Traversal pattern treats a 2D grid as a graph. Each cell is a node, and its neighbours are the cells directly above, below, left and right of it — all derivable from the coordinates alone.

Once you see the grid that way, questions about regions become questions about connected regions, and a single depth-first or breadth-first search from any node discovers the entire region containing it.

## Example

Count the islands in this grid, where `1` is land and `0` is water:

```plaintext
1 1 0 0 0
1 1 0 0 0
0 0 1 0 0
0 0 0 1 1
```

Scan cell by cell. Every time you land on a `1` that has not been claimed yet, that cell begins a region nobody has seen before, so increment the count and then flood outwards to claim everything attached to it:

```plaintext
(0,0) is land, unclaimed  ->  count = 1, flood claims (0,0) (0,1) (1,0) (1,1)

(0,1) already claimed         skip
(1,0) already claimed         skip
(2,2) is land, unclaimed  ->  count = 2, flood claims (2,2)
(3,3) is land, unclaimed  ->  count = 3, flood claims (3,3) (3,4)

answer = 3
```

The outer scan finds seeds; the flood consumes regions. Neither ever revisits a cell.

## Core idea

Adjacency comes from arithmetic, not from a data structure:

```plaintext
neighbours of (r, c)  =  (r-1, c)  (r+1, c)  (r, c-1)  (r, c+1)
```

A depth-first or breadth-first search launched from any node reaches exactly the nodes in its connected region, so "find the region containing this node" and "search from this node" are the same operation.

Marking cells as visited is what keeps this cheap, and the accounting is worth being precise about. The outer loop inspects all `m × n` cells. Each search also walks cells — but because the visited marks are never reset between searches, every cell is entered by at most one search in the entire run. All searches therefore cost `m × n` *in total*, not `m × n` each:

```plaintext
outer scan            m × n cell inspections
all searches combined m × n cell entries, shared across every region
                      ────────────────────
total                 O(m × n)
```

Without the marking, a cell reachable from four directions would be explored four times over, and each of those explorations would re-explore its own neighbours — the cost stops being linear immediately.

## Variations

### 1. Count the connected regions

Scan for unclaimed nodes, run a depth-first search from each one, and increment a counter per search. The traversal returns nothing; its only job is to stop the outer loop from counting the same region twice. This is [Number of Islands](./01-number-of-islands.md).

### 2. Measure each connected region

The same scan, but the depth-first search returns a value that the caller aggregates — usually a size, summed from the recursive calls. This is [Biggest Island](./02-biggest-island.md), where the caller keeps a running maximum.

Once the traversal can return something, the pattern stretches a long way: perimeter, sum of values, bounding box, and shape signatures are all the same graph search with a different accumulator.

### 3. Flood from a given seed

No outer scan at all — the problem hands you the starting node, and you run the traversal exactly once. This is [Flood Fill](./03-flood-fill.md).

Here the visited marker and the output are the same thing: recoloring a cell both records the answer and stops the traversal re-entering it.

### 4. regions qualified by a boundary condition

Count only the connected regions that satisfy some global property, most often "does not touch the edge of the grid". This is [Number of Closed Islands](./04-number-of-closed-islands.md).

Two approaches, and they are worth knowing both. Either disqualify first — run depth-first search inward from every border node to remove the regions that reach the edge, which reduces the problem to variation 1 — or let the traversal report back whether it ever stepped onto a border node.

## Templates

Recursive depth-first search, which is the shortest to write:

```python
def dfs(grid, r, c):
    if r < 0 or r >= len(grid) or c < 0 or c >= len(grid[0]):
        return                      # bounds first, before any indexing

    if grid[r][c] != TARGET:
        return                      # wrong value, or already claimed

    grid[r][c] = CLAIMED            # mark BEFORE recursing, never after

    for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
        dfs(grid, r + dr, c + dc)
```

Iterative breadth-first search, for when recursion depth is a risk or the problem needs distance:

```python
from collections import deque


def bfs(grid, r, c):
    queue = deque([(r, c)])
    grid[r][c] = CLAIMED            # mark on PUSH, not on pop

    while queue:
        row, col = queue.popleft()
        for dr, dc in ((1, 0), (-1, 0), (0, 1), (0, -1)):
            nr, nc = row + dr, col + dc
            if 0 <= nr < len(grid) and 0 <= nc < len(grid[0]) and grid[nr][nc] == TARGET:
                grid[nr][nc] = CLAIMED
                queue.append((nr, nc))
```

The driver that turns either one into a region count:

```python
regions = 0

for r in range(len(grid)):
    for c in range(len(grid[0])):
        if grid[r][c] == TARGET:    # unclaimed, so it seeds a new region
            regions += 1
            dfs(grid, r, c)         # or bfs(grid, r, c)
```

Both traversals visit the same nodes and cost the same. Choose depth-first for brevity, breadth-first when the grid is large enough that the call stack is a concern, or when you need nodes grouped by distance from the seed.

## Recognize it when

- The graph is already there and you are about to build it anyway. Grid adjacency is arithmetic on coordinates, so constructing an adjacency list from a matrix is wasted work — the matrix is the adjacency list.
- The question is about maximal connected regions rather than individual cells. A plain double loop answers anything local; the moment the answer depends on what a cell is *connected* to, a graph search has to walk the connection.
- The same node can be reached from more than one direction. That is what forces a visited marker, and the marker is precisely what collapses the cost from exponential to linear.
- A property is local to define but global in extent. "Is this cell land" needs one lookup; "how large is the connected region containing it" needs the whole region. Graph search is the mechanism that accumulates local adjacency into a region-wide answer.
- Your fallback plan is to sweep the grid repeatedly until nothing changes. Fixed-point iteration over a matrix is almost always one depth-first search per seed written the expensive way.

## Reach for something else when

- You need the fewest steps between two nodes: still a graph traversal, but it must be breadth-first, and you are measuring distance rather than identifying regions.
- Moving between nodes has a cost that varies: Dijkstra, or 0-1 BFS when the only costs are 0 and 1.
- Nodes are added or removed and connectivity is re-queried as you go: Union-Find merges incrementally, whereas a depth-first search would have to re-run from scratch after every change.
- regions are defined by shape rather than by connectivity, such as rectangles or diagonal-only runs: the decomposition is geometric and graph search will not find it.
- Each cell's answer depends only on itself or on a fixed window around it: a double loop, or dynamic programming over the grid.

## Pitfalls

- Marking a node visited after recursing rather than before. Two neighbours both step into the same unmarked node and the depth-first search never bottoms out.
- In breadth-first search, marking on dequeue instead of on enqueue. The same node gets pushed by every neighbour that sees it, which still terminates but multiplies the queue size and the work.
- Indexing before bounds-checking. `grid[r][c]` with a negative `r` silently wraps around to the other end of the grid in Python rather than raising, so the bug surfaces as a wrong answer rather than a crash.
- Recursion depth. A `300` × `300` grid of solid land is `90,000` nested frames against Python's default limit of `1000` — reach for the iterative breadth-first search template rather than raising the limit.
- In Flood Fill, forgetting the case where the new color already equals the starting color. The recoloring is then invisible to the visited guard, so nothing ever looks visited and the traversal does not terminate.
- Including diagonals when the problem says horizontally and vertically only, which silently merges regions that should stay separate.

## Key takeaways

- A grid is a graph whose edges are arithmetic; never build an adjacency list from one.
- One depth-first or breadth-first search from a node visits exactly its connected region, so counting regions is counting the seeds that start a search.
- Visited marks are shared across every search, which is why the whole scan is $O(m \times n)$ rather than that per region.
- Mark on entry — before recursing, or on push rather than pop — or the traversal revisits and may not terminate.
- Depth-first and breadth-first explore the same nodes for the same cost; pick breadth-first for deep grids or when distance matters.
- Making the traversal return a value turns counting regions into measuring them, which is most of this pattern's range.

## Problems

See [PROBLEMS.md](./PROBLEMS.md) for the full list, including a short set to revise when time is tight.
