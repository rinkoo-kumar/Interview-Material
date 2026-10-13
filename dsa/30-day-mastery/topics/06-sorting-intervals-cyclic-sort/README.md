# Sorting, Intervals, and Cyclic Sort

## What it is

Sorting puts values in order so nearby items can be compared. Interval algorithms sort ranges by a boundary and then merge or select them. Cyclic sort places bounded values into their matching indices.

## Who benefits

Anyone solving overlapping schedules, range merging, ordering, or missing/duplicate-number problems where values are known to lie in a small index range.

## When to use it

Sort intervals when overlap depends on start/end order. Consider cyclic placement when an array of length `n` contains values in a range like `1..n` and asks for a missing or duplicate value.

## Why it helps

Sorting makes relationships easier to see: after ordering by start, an interval can overlap only the latest merged interval. Cyclic placement can achieve linear work without a separate frequency table.

## How to apply it

For intervals: sort by start, track the current merged range, extend it on overlap, otherwise emit it and start a new range. For cyclic placement: repeatedly swap a value toward its correct index, stopping on duplicates or already-correct values.

## Complexity and trade-offs

Comparison sorting typically costs `O(n log n)` time. Merging costs `O(n)` after sorting and often `O(n)` output space. Cyclic placement is usually `O(n)` time and `O(1)` extra space but may mutate the input.

## Common mistakes

- Sorting intervals by the wrong endpoint.
- Treating touching interval endpoints inconsistently with the problem's definition.
- Creating infinite swaps when a duplicate value is already in its target position.

## Practice

Merge Intervals, Insert Interval, Non-overlapping Intervals, and First Missing Positive. See the [top-problem guide](../../../11-top-50-dsa-interview-problems.md).
