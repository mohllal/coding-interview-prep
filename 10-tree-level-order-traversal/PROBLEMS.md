# Tree Level Order Traversal: Problems

## If I'm in a hurry

Five problems covering every way the level-by-level loop gets bent.

| #  | Problem                                                                        | Why this one                                            | Difficulty |
|----|--------------------------------------------------------------------------------|---------------------------------------------------------|------------|
| 01 | [Binary Tree Level Order Traversal](./01-binary-tree-level-order-traversal.md) | The level-size record everything else reuses            | Medium     |
| 03 | [Zigzag Traversal](./03-zigzag-traversal.md)                                   | Change how a level is recorded, not how it is traversed | Medium     |
| 07 | [Even Odd Tree](./07-even-odd-tree.md)                                         | Comparing each node with its left neighbour             | Medium     |
| 13 | [Connect All Level Order Siblings](./13-connect-all-level-order-siblings.md)   | When you don't need level boundaries at all             | Medium     |
| 14 | [Right View of a Binary Tree](./14-right-view-of-a-binary-tree.md)             | Selecting one node per level, and why paths don't work  | Medium     |

## All problems

Problems marked *(unsolved)* have the statement but no solution yet.

| #    | Problem                                                                                   | Variant                            | Difficulty |
|------|-------------------------------------------------------------------------------------------|------------------------------------|------------|
| 01   | [Binary Tree Level Order Traversal](./01-binary-tree-level-order-traversal.md)            | Collect every level                | Medium     |
| 02   | [Reverse Level Order Traversal](./02-reverse-level-order-traversal.md)                    | Levels stored bottom-up            | Medium     |
| 03   | [Zigzag Traversal](./03-zigzag-traversal.md)                                              | Alternate direction per level      | Medium     |
| 04   | [Level Averages in a Binary Tree](./04-level-averages-in-a-binary-tree.md)                | Aggregate per level (average)      | Easy       |
| 05   | [Find Largest Value in Each Tree Row](./05-find-largest-value-in-each-tree-row.md)        | Aggregate per level (maximum)      | Medium     |
| 06   | [Maximum Level Sum of a Binary Tree](./06-maximum-level-sum-of-a-binary-tree.md)          | Aggregate per level, keep the best | Medium     |
| 07   | [Even Odd Tree](./07-even-odd-tree.md)                                                    | Validate neighbours within a level | Medium     |
| 08   | [Minimum Depth of a Binary Tree](./08-minimum-depth-of-a-binary-tree.md)                  | Stop at the first leaf             | Easy       |
| 08.1 | [Maximum Depth of a Binary Tree](./08.1-maximum-depth-of-a-binary-tree.md)                | Count every level                  | Easy       |
| 09   | [Level Order Successor](./09-level-order-successor.md) *(unsolved)*                       | Stop right after the key           | Easy       |
| 10   | [Connect Level Order Siblings](./10-connect-level-order-siblings.md) *(unsolved)*         | Link nodes within a level          | Medium     |
| 11   | [Maximum Width of Binary Tree](./11-maximum-width-of-binary-tree.md) *(unsolved)*         | Position index per node            | Medium     |
| 12   | [N-ary Tree Level Order Traversal](./12-n-ary-tree-level-order-traversal.md) *(unsolved)* | Any number of children             | Medium     |
| 13   | [Connect All Level Order Siblings](./13-connect-all-level-order-siblings.md)              | Link nodes across levels           | Medium     |
| 14   | [Right View of a Binary Tree](./14-right-view-of-a-binary-tree.md)                        | Pick the last node per level       | Medium     |
| 14.1 | [Left View of a Binary Tree](./14.1-left-view-of-a-binary-tree.md)                        | Pick the first node per level      | Medium     |
