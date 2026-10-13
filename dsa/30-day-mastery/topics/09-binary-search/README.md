# Binary Search

## What it is

Binary search repeatedly discards half of an ordered search range. The search space can be array indices or possible answers to an optimization problem.

## Who benefits

Anyone searching sorted data or solving a problem where “can this answer work?” changes monotonically from false to true (or true to false).

## When to use it

Use it when the data is sorted, or when a candidate answer has a monotonic feasibility rule. Boundary questions—first true, last false, insertion point—are common variations.

## Why it helps

Each step halves the remaining candidates, so a search over `n` possibilities takes `O(log n)` checks rather than `O(n)` checks.

## How to apply it

Define `low`, `high`, and the meaning of the loop invariant. Choose a midpoint, decide which half still contains the answer, and ensure every loop step shrinks the range. For answer search, write a correct feasibility test first.

## Complexity and trade-offs

There are `O(log n)` iterations. Total time is `O(log n)` times the cost of one check; an `O(n)` feasibility test makes the total `O(n log n)`.

## Common mistakes

- Infinite loops from incorrect inclusive/exclusive bounds.
- Searching an unsorted range without a monotonic property.
- Returning an arbitrary match when the task asks for a boundary.

## Practice

Binary Search, Search in Rotated Sorted Array, Find Minimum in Rotated Sorted Array, and Koko Eating Bananas. See [chapter 5](../../../05-binary-search.md).
