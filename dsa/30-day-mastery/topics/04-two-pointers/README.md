# Two Pointers

## What it is

Two indices move through a sequence together. They may start at opposite ends, move in the same direction, or track positions in two different sequences.

## Who benefits

Anyone working with sorted arrays, pairs, triplets, palindromes, or linked-list positions. It is particularly useful when an `O(n)` scan is possible instead of trying every pair.

## When to use it

Look for sorted input, a pair or triplet condition, a boundary between valid and invalid values, or a need to compare both ends. Same-direction pointers are useful for filtering or fast/slow traversal.

## Why it helps

Each pointer can move forward without revisiting old choices. With a reasoned movement rule, two pointers often reduce `O(n^2)` searching to `O(n)` after any required sorting.

## How to apply it

Write down what each pointer represents. On each step, decide which pointer can safely move based on the current values; prove that moving it cannot discard a better answer.

## Complexity and trade-offs

Many two-pointer scans are `O(n)` time and `O(1)` extra space. Sorting first changes total time to `O(n log n)` and may change input order.

## Common mistakes

- Using the pattern on unsorted data without a valid movement rule.
- Missing duplicates when collecting unique pairs or triplets.
- Moving both pointers when only one move is justified.

## Practice

Valid Palindrome, Two Sum II, 3Sum, and Container With Most Water. See [chapter 2](../../../02-two-pointers-and-sliding-window.md).
