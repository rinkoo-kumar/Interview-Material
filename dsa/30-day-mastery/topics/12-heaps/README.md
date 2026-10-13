# Heaps and Priority Queues

## What it is

A heap keeps the smallest or largest item at its top. A priority queue uses that rule to repeatedly retrieve the next most important item.

## Who benefits

Anyone processing streams, scheduling by priority, finding top/bottom K items, or merging already-sorted sources.

## When to use it

Use a heap when you need repeated access to an extreme value but do not need the entire collection fully sorted. Two heaps are useful for a running median; one heap per source supports K-way merge.

## Why it helps

Insertions and removals take `O(log n)`, often better than sorting the whole collection after every update. For top K, a heap of size K can keep memory bounded.

## How to apply it

Choose min-heap or max-heap semantics. Keep only the needed K values when possible. For K-way merge, insert the current first item of each source and add that source's next item after removal.

## Complexity and trade-offs

Peek is `O(1)`; push/pop is `O(log n)`; building a heap from a collection is `O(n)`. Space depends on how many items are retained.

## Common mistakes

- Sorting all values when only K are needed.
- Forgetting to rebalance the two heaps in a median tracker.
- In Python, tuples in a heap may compare tied node objects unless a unique tie-breaker is included.

## Practice

Kth Largest Element, Top K Frequent Elements, Find Median from Data Stream, and Merge K Sorted Lists. See [chapter 7](../../../07-heaps-and-priority-queues.md).
