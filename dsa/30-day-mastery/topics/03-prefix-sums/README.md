# Prefix Sums

## What it is

A prefix sum stores the cumulative total up to each position. The difference between two cumulative totals gives the sum of the values between those positions.

## Who benefits

Learners working with repeated range-sum questions, subarrays, or counts of continuous sections. It also helps when a subarray's sum is not monotonic, so a sliding window cannot safely shrink or expand.

## When to use it

Use prefix sums for many range queries or when asked to count continuous subarrays with a target sum. A hash map of earlier prefix sums can count matching ranges, including when numbers are negative.

## Why it helps

Instead of adding the same range again and again, preprocess cumulative totals. For target `k`, if the current prefix is `s`, an earlier prefix `s-k` marks a range summing to `k`.

## How to apply it

Start with prefix `0` occurring once. For each value, update the running sum, add the number of prior occurrences of `sum-k` to the answer, then record the current sum.

## Complexity and trade-offs

Building prefix values costs `O(n)` time and usually `O(n)` space. A hash-map counting scan is expected `O(n)` time and `O(n)` space.

## Common mistakes

- Forgetting the initial zero prefix, which represents a range starting at index zero.
- Updating the map before counting and accidentally counting an empty range when `k=0`.
- Using a standard sliding window when negative numbers break its monotonic behavior.

## Practice

Range Sum Query and Subarray Sum Equals K. See the [solutions pack](../../../solutions-pack-1-arrays-strings-sliding-window.md).
