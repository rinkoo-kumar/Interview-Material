# Tries (Prefix Trees)

## What it is

A trie stores words one character at a time. Shared prefixes use shared paths; a marker distinguishes a complete stored word from a prefix.

## Who benefits

Anyone building prefix search, autocomplete, dictionary lookup, or word-search features over many related strings.

## When to use it

Consider a trie when many queries ask whether words exist or whether any word begins with a prefix. A hash set can be simpler when only whole-word membership matters.

## Why it helps

A lookup depends on the query length rather than scanning every stored word. Sharing common prefixes can also avoid storing the same prefix repeatedly.

## How to apply it

Create a child node for each missing character as you insert. Mark the final node as a word ending. For exact search, require the end marker; for prefix search, only require the path to exist.

## Complexity and trade-offs

Insert and lookup take `O(L)` for a string of length `L`. Memory can be large because each distinct prefix character needs a node/link.

## Common mistakes

- Treating an existing prefix as a complete word.
- Forgetting to mark the terminal node on insertion.
- Ignoring case, alphabet, or memory constraints.

## Practice

Implement Trie, Word Search II, and Replace Words. See the trie section in [chapter 6](../../../06-trees-and-tries.md).
