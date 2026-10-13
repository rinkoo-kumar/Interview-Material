# Union-Find (Disjoint Set Union)

## What it is

Union-Find keeps track of groups that do not overlap. `find` identifies a group's representative; `union` combines two groups.

## Who benefits

Anyone solving repeated connectivity queries, undirected cycle detection, or grouping relationships as edges are added.

## When to use it

Use it when groups only merge and you need to ask whether two items are connected. Prefer BFS/DFS when you need the actual path or frequent deletions.

## Why it helps

Path compression and union by size/rank make operations nearly constant time in practice, without traversing the whole graph for each connectivity query.

## How to apply it

Start each item as its own parent. Find roots before union; attach the smaller tree under the larger. Compress paths during find. If an edge's endpoints already share a root, that edge closes a cycle.

## Complexity and trade-offs

With both optimizations, operations are amortized `O(alpha(n))`, where `alpha` grows so slowly it is effectively constant for practical inputs. Storage is `O(n)`.

## Common mistakes

- Comparing raw parent values instead of their roots.
- Updating the wrong root or forgetting to update component size/rank.
- Using it when components must split after removing edges.

## Practice

Redundant Connection, Number of Provinces, and Accounts Merge. See [chapter 8](../../../08-graphs-and-union-find.md).
