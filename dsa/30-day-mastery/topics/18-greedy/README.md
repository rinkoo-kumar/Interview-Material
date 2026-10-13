# Greedy Algorithms

## What it is

A greedy algorithm makes the choice that looks best now and continues. It does not normally revisit earlier choices.

## Who benefits

Anyone solving scheduling, interval selection, reachability, or optimization problems where a local decision may lead to a global optimum.

## When to use it

Use greedy only when you can justify why a locally best choice is safe—often by an exchange argument or an invariant. A pattern resemblance alone is not proof.

## Why it helps

When its correctness condition holds, greedy solutions can be much simpler and faster than exploring all choices or filling a DP table.

## How to apply it

State the candidate choice and why it cannot hurt an optimal solution. Prove the invariant, then implement and test boundary cases. If the proof is unclear, compare with DP or exhaustive search on small inputs.

## Complexity and trade-offs

Many greedy solutions are `O(n)` or `O(n log n)` when sorting is required, but complexity varies. The main trade-off is that an incorrect local rule can fail subtly.

## Common mistakes

- Assuming “take the biggest/smallest first” is always correct.
- Confusing a plausible explanation with a correctness proof.
- Ignoring ties or endpoint conventions in intervals.

## Practice

Jump Game, Gas Station, Non-overlapping Intervals, and meeting-room scheduling. See [chapter 10](../../../10-backtracking-and-greedy.md).
