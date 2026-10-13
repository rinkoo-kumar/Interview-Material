# Matrices and Grids

## What it is

A matrix stores values in rows and columns. Many grid problems treat each cell as a graph node connected to neighboring cells.

## Who benefits

Anyone solving image-like data, board games, maze traversal, connected regions, or row/column search problems.

## When to use it

Use direct row/column loops for transformations and scans. Use BFS/DFS for regions and reachability. Use binary search only when rows/columns satisfy the required sorted-order property.

## Why it helps

Thinking of a grid as a graph reuses familiar traversal patterns. Clear boundary checks prevent invalid neighbor access.

## How to apply it

Define the four (or eight) directions once. Check bounds before reading a neighbor. Mark cells visited either in a separate set or by changing the grid only if mutation is allowed.

## Complexity and trade-offs

A full traversal of an `R × C` grid is `O(RC)` time and may use `O(RC)` visited/queue/stack space.

## Common mistakes

- Swapping row and column limits on non-square grids.
- Marking a cell visited too late, allowing it to enter a queue repeatedly.
- Mutating the input when the caller expects it unchanged.

## Practice

Number of Islands, Flood Fill, Rotting Oranges, Search a 2D Matrix, and Unique Paths. See [chapter 8](../../../08-graphs-and-union-find.md) and [chapter 9](../../../09-dynamic-programming.md).
