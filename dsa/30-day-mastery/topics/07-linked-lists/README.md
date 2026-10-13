# Linked Lists

## What it is

A linked list stores values in nodes. Each node points to the next node, so nodes are connected by links rather than adjacent array positions.

## Who benefits

Anyone needing pointer practice, in-place list changes, or cycle detection. Interviews use linked lists to test whether a learner can maintain references without losing part of the structure.

## When to use it

Use list techniques when input is a linked list or when the task asks for reversal, merging, midpoint, cycle, or removing a node from an unknown-length chain.

## Why it helps

Once the correct node is known, changing a link can be constant time. Fast/slow pointers can find a midpoint or cycle without storing every visited node.

## How to apply it

Use a dummy head for operations that may replace the first node. For reversal, save the next node before redirecting a link. For midpoint/cycle detection, move slow one step and fast two steps.

## Complexity and trade-offs

Most full traversals take `O(n)`. In-place reversal uses `O(1)` extra space; recursive solutions may use `O(n)` call-stack space.

## Common mistakes

- Overwriting `next` before saving the remainder of the list.
- Mishandling the head or an empty/single-node list.
- Confusing cycle detection with finding the cycle's entry point.

## Practice

Reverse Linked List, Linked List Cycle, Middle of the Linked List, Remove Nth Node From End, and Reorder List. See [chapter 3](../../../03-linked-lists.md).
