# Complexity and Problem-Solving Basics

## What it is

Complexity describes how an algorithm's work or memory use grows as the input grows. Big-O focuses on growth, not the number of seconds on one computer.

## Who benefits

Every programmer benefits from estimating cost. It is especially useful when interview constraints are large, performance matters, or two correct solutions need to be compared.

## When to use it

Use complexity analysis whenever you choose between approaches, before writing code and again after it works. Check the input size and ask whether a quadratic or exponential approach can fit.

## Why it helps

A correct algorithm can still be unusable on large inputs. Complexity helps you spot repeated work and choose a solution that scales.

## How to reason about it

1. Let `n` represent the input size.
2. Count how many times the main work can happen.
3. Keep the fastest-growing term: one loop is often `O(n)`, two nested loops often `O(n^2)`.
4. Count extra data structures separately for space.
5. State assumptions: recursion uses call-stack space; sorting has its own cost.

Common growth rates from smaller to larger: `O(1)`, `O(log n)`, `O(n)`, `O(n log n)`, `O(n^2)`, `O(2^n)`, `O(n!)`.

## Common mistakes

- Calling every loop `O(n)`: nested loops may be `O(n^2)`.
- Counting the input itself as extra space when only auxiliary space is requested.
- Assuming a hash table is always constant-time in every theoretical case; describe expected/average behavior.
- Optimizing before you have a correct baseline and a reason to optimize.

## Practice

Take a simple duplicate-checking solution using nested loops, then improve it with a set. Explain the time-space trade-off. See the [DSA track index](../../../../README.md).
