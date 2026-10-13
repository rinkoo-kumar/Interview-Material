# Backtracking

## What it is

Backtracking explores choices step by step. It keeps a partial answer, tries a choice, explores what follows, then undoes that choice.

## Who benefits

Anyone solving “find all” combinations, arrangements, paths, or valid boards where each decision changes what can be chosen next.

## When to use it

Use it when the answer is a set of possibilities and constraints can reject partial solutions early. If only the best value is required, compare with dynamic programming or greedy methods.

## Why it helps

It systematically explores a decision tree and prunes branches that cannot produce valid answers. This is more organized than generating every complete candidate blindly.

## How to apply it

Define state, choices, constraints, and the goal. At each call: check if complete, try each valid choice, recurse, then undo the choice. Avoid mutable-state leaks between branches.

## Complexity and trade-offs

The number of candidates is often exponential (`O(2^n)`) or factorial (`O(n!)`). Pruning helps on real inputs but may not change worst-case growth.

## Common mistakes

- Forgetting to undo a mutation after recursion.
- Reusing an index when permutations require each item once.
- Duplicate results when input values repeat and duplicates are not skipped.

## Practice

Subsets, Permutations, Combination Sum, Word Search, and N-Queens. See [chapter 10](../../../10-backtracking-and-greedy.md).
