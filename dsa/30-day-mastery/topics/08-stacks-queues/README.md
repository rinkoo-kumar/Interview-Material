# Stacks, Queues, and Monotonic Structures

## What it is

A stack is last-in, first-out; a queue is first-in, first-out. A monotonic stack or deque keeps values (or their indices) in increasing or decreasing order as items arrive.

## Who benefits

Anyone working with nested structure, undo-like behavior, level-order processing, next-greater/smaller questions, or moving-window extrema.

## When to use it

Use a stack for matching brackets and “nearest previous/next greater” problems. Use a queue for breadth-first traversal and a deque for efficiently adding/removing from both ends.

## Why it helps

Items can be removed once they are no longer useful. In a monotonic stack, each item is pushed and popped at most once, often making an apparently nested process linear.

## How to apply it

Store indices when the answer depends on distance or a later position. Before pushing, pop items that violate the chosen monotonic order. For BFS, mark nodes visited when enqueuing to avoid duplicates.

## Complexity and trade-offs

Typical stack/queue operations are `O(1)`. A monotonic scan is usually `O(n)` time and `O(n)` space.

## Common mistakes

- Choosing increasing vs decreasing order without defining the question's comparison.
- Forgetting equal values and duplicate handling.
- Removing an item before recording the answer it enables.

## Practice

Valid Parentheses, Daily Temperatures, Largest Rectangle in Histogram, and Sliding Window Maximum. See [chapter 4](../../../04-stacks-and-queues.md).
