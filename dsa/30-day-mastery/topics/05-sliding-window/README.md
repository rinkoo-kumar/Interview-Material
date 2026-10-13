# Sliding Window

## What it is

A window is a contiguous range of an array or string. Sliding-window code moves its left and right boundaries while updating information about what is inside.

## Who benefits

Learners solving contiguous substring/subarray problems involving lengths, counts, sums, uniqueness, or a minimum/maximum valid range.

## When to use it

Use a fixed window when its size is given. Use a variable window when you can expand until a condition is met and shrink while the condition remains satisfied. For sum constraints, check whether negative values invalidate the usual movement rule.

## Why it helps

Adding the new right item and removing the old left item avoids recomputing each range from scratch. A common scan is `O(n)` instead of `O(n^2)`.

## How to apply it

1. Expand the right boundary and update counts or sum.
2. While the window violates the rule (or remains valid, for a shortest-window search), move the left boundary and update the same state.
3. Record the best answer only when the window meets the required condition.

## Complexity and trade-offs

The two boundaries usually move at most `n` times, giving `O(n)` time. Frequency maps may use `O(k)` space for the distinct items in the window.

## Common mistakes

- Updating the answer for an invalid window.
- Forgetting to remove departing characters from the count map.
- Assuming a variable window works for negative values with a sum threshold.

## Practice

Longest Substring Without Repeating Characters, Minimum Window Substring, and Sliding Window Maximum. See [chapter 2](../../../02-two-pointers-and-sliding-window.md).
