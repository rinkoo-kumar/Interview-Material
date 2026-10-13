# Trees and Traversal

## What it is

A tree is a hierarchy of nodes connected by parent-child links. DFS explores down a branch; BFS visits nodes level by level. A binary search tree also orders values by a rule.

## Who benefits

Anyone working with hierarchical data, parse structures, recursive relationships, or problems asking about paths, ancestors, depth, and levels.

## When to use it

Use DFS for subtree, path, and ancestor reasoning. Use BFS for level order and shortest edge-count distance in an unweighted tree. Use BST ordering only when the input guarantees it.

## Why it helps

Each node can be solved from results of its children, or processed in a meaningful level order. Traversing each node once is often enough.

## How to apply it

Choose preorder, inorder, postorder, or level order based on when a node's value is needed. State what a recursive call returns and its base case before coding. Track a global answer only if a single subtree return cannot express it.

## Complexity and trade-offs

Visiting all `n` nodes costs `O(n)`. DFS uses `O(h)` call-stack space for tree height `h`; BFS can use `O(w)` space for maximum level width.

## Common mistakes

- Mixing the recursive return value with the global answer.
- Assuming a tree is balanced or a BST without evidence.
- Missing empty-tree and one-node cases.

## Practice

Maximum Depth, Level Order Traversal, Lowest Common Ancestor, Binary Tree Maximum Path Sum, and Serialize/Deserialize. See [chapter 6](../../../06-trees-and-tries.md).
