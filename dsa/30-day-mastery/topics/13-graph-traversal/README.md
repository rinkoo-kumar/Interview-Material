# Graph Traversal: BFS and DFS

## What it is

A graph contains vertices connected by edges. BFS uses a queue to explore by distance in edges; DFS follows a path deeply before returning.

## Who benefits

Anyone solving network connectivity, maze/grid exploration, reachability, components, or unweighted shortest-path problems.

## When to use it

Use BFS for the fewest edges in an unweighted graph or level-by-level exploration. Use DFS for reachability, components, cycle checks, and recursive exploration. Choose adjacency lists for sparse graphs.

## Why it helps

Both traversals can visit every vertex and edge once. They turn “can I get from here to there?” into a systematic process instead of repeated guesses.

## How to apply it

Represent neighbors consistently. Track visited vertices; mark them when adding to the queue/stack. For disconnected graphs, start a traversal from every unvisited vertex.

## Complexity and trade-offs

With adjacency lists, traversal takes `O(V + E)` time and `O(V)` auxiliary space. An adjacency matrix may use `O(V^2)` space.

## Common mistakes

- Forgetting that graphs can contain cycles.
- Only starting at one node when the graph may be disconnected.
- Confusing shortest path by number of edges with shortest weighted path.

## Practice

Number of Islands, Clone Graph, Rotting Oranges, and Word Ladder. See the graph chapters [chapter 8](../../../08-graphs-and-union-find.md) and [tree/graph solutions](../../../solutions-pack-2-trees-graphs-heaps.md).
