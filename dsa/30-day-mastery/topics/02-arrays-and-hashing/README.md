# Arrays and Hashing

## What it is

Arrays store items in order and provide fast access by index. A hash map or set stores keys so code can usually find or count them quickly.

## Who benefits

Anyone solving data lookup, counting, grouping, or sequence problems. This is a strong first pattern for interview learners because many problems become simpler when past values are remembered.

## When to use it

Use an array when order or index matters. Use a hash map/set when you need fast membership checks, counts, grouping, or a connection between a value and its earlier position.

## Why it helps

Remembering earlier values can replace repeatedly scanning the input. A nested-loop pair search is often `O(n^2)`; a hash lookup can reduce expected time to `O(n)` at the cost of extra memory.

## How to recognize and apply it

Clues: “seen before,” “how many,” “group by,” “find a matching value,” or “first/last index.” For Two Sum, scan once, look for the needed complement, then record the current value and index.

## Complexity and trade-offs

Array indexing is `O(1)`. A full scan is `O(n)`. Hash lookup/insert is expected `O(1)` and usually costs `O(n)` extra space across `n` items.

## Common mistakes

- Recording the current value before checking whether it pairs with itself.
- Forgetting duplicate counts or overwriting an index when the first index is needed.
- Assuming a set preserves the order needed by the answer.

## Practice

Two Sum, Contains Duplicate, Group Anagrams, and Top K Frequent Elements. See [chapter 1](../../../01-arrays-and-hashing.md).
