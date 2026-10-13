# Shortest Paths

## What it is

Shortest-path algorithms find a least-cost route from one node to another. The correct algorithm depends on whether edges are unweighted, non-negative weighted, or may be negative.

## Who benefits

Anyone solving route planning, network delay, minimum-cost transitions, or graph problems that ask for distance rather than just reachability.

## When to use it

- **BFS:** every edge has equal cost (unweighted shortest path).
- **Dijkstra:** edge weights are non-negative.
- **Bellman–Ford:** negative weights may exist; it can also detect a reachable negative cycle.

## Why it helps

Each method processes candidate routes in a way that avoids checking every possible path independently. Picking the wrong method can silently produce incorrect distances.

## How to apply it

Store distances, initialize the source to zero, and relax outgoing edges. In Dijkstra, use a min-heap and ignore stale entries; only finalize distance under its non-negative-weight condition.

## Complexity and trade-offs

BFS is `O(V + E)`. Heap-based Dijkstra is typically `O((V + E) log V)`. Bellman–Ford is `O(VE)` and handles negative edges.

## Common mistakes

- Using Dijkstra with negative-weight edges.
- Forgetting to skip stale heap entries.
- Returning a result before confirming all required nodes are reachable.

## Practice

Network Delay Time, Cheapest Flights Within K Stops, and shortest path in an unweighted grid. See [chapter 8](../../../08-graphs-and-union-find.md).
