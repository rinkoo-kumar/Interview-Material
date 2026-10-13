# Dynamic Programming

## What it is

Dynamic programming (DP) solves a problem by defining smaller states, reusing their answers, and combining them to solve larger states. It may use recursion with memoization or iterative tabulation.

## Who benefits

Anyone facing repeated subproblems and an answer that can be built from smaller answers—especially counting, minimum/maximum cost, and sequence alignment problems.

## When to use it

Look for overlapping subproblems and optimal substructure. If a choice affects future choices, define a state that captures enough information to make the remaining problem independent of the past.

## Why it helps

Memoization prevents recalculating the same state. Tabulation makes the order of computation explicit. Both can turn exponential recursion into polynomial work when the number of states is manageable.

## How to apply it

1. Define what `dp[state]` means in plain words.
2. Write the transition/recurrence.
3. Set base cases.
4. Choose a valid computation order.
5. Return the state corresponding to the original problem.
6. Estimate number of states times work per state.

## Complexity and trade-offs

Time is usually `number of states × transitions per state`; space is the number of stored states. Space can sometimes be reduced if older states are no longer needed.

## Common mistakes

- Creating a state that omits information needed by future decisions.
- Incorrect base cases or loop order.
- Optimizing space before the recurrence is correct.

## Practice

Climbing Stairs, House Robber, Coin Change, Longest Increasing Subsequence, Word Break, and Edit Distance. See [chapter 9](../../../09-dynamic-programming.md) and [solution pack 3](../../../solutions-pack-3-dp-and-backtracking.md).
