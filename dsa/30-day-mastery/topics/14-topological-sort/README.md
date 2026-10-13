# Topological Sorting

## What it is

A topological order is a linear ordering of a directed acyclic graph where every prerequisite appears before what depends on it.

## Who benefits

Anyone handling build steps, course prerequisites, job dependencies, or any directed dependency graph.

## When to use it

Use it when tasks have directed “must happen before” relationships. It only works when there is no directed cycle; if a cycle exists, no valid complete order exists.

## Why it helps

The ordering makes dependencies explicit and gives a valid execution sequence. Kahn's algorithm also detects cycles by checking whether it processed every vertex.

## How to apply it

For Kahn's algorithm, count incoming edges, enqueue all zero-indegree nodes, repeatedly emit one and reduce its neighbors' indegrees. If fewer than all nodes are emitted, a cycle exists. DFS postorder is another approach.

## Complexity and trade-offs

Adjacency-list implementations take `O(V + E)` time and space.

## Common mistakes

- Reversing the direction of prerequisite edges.
- Failing to detect that the produced ordering omits cyclic nodes.
- Expecting a unique order when several nodes can be chosen at once.

## Practice

Course Schedule I and II, Alien Dictionary, and build/dependency ordering. See [chapter 8](../../../08-graphs-and-union-find.md).
