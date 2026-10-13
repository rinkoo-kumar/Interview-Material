# 30-Day DSA Mastery Roadmap

A practical month-long plan for learning to **recognize patterns, explain trade-offs, and solve unfamiliar problems**. It is a focused study plan, not a guarantee of mastery: keep practising after day 30.

## Who this plan is for

This plan is for learners who know basic programming (variables, loops, functions, lists, and dictionaries) and want a structured path through common data structures and algorithms. It works for interview preparation and for anyone who wants to reason about efficient code.

Choose one language for the first pass. The existing DSA chapters contain Python and Java examples; the ideas apply in either language.

## How to use the plan

Aim for about **2–3 focused hours each day**. For each problem:

1. Restate the input, output, and constraints in your own words.
2. Try a simple solution first; write down its time and space costs.
3. Identify the pattern and explain why it fits before coding.
4. Implement, test edge cases, and explain the code aloud.
5. If stuck, study the relevant guide, then close it and solve again from memory.
6. Keep a mistake log: missed edge case, wrong pattern, invariant, or complexity.

Do not measure progress by problems completed alone. You are improving when you can explain **why** an approach works and when it would not.

## The 30-day schedule

| Day | Focus | Suggested work |
|---:|---|---|
| 1 | [Complexity and problem-solving basics](topics/01-complexity/README.md) | Big-O, input constraints, brute force, and a baseline solution |
| 2 | [Arrays and hashing](topics/02-arrays-and-hashing/README.md) | Two Sum, frequency counts, duplicate detection |
| 3 | [Prefix sums](topics/03-prefix-sums/README.md) | Range sums and Subarray Sum Equals K |
| 4 | [Two pointers](topics/04-two-pointers/README.md) | Palindrome checks, sorted pairs, 3Sum |
| 5 | [Sliding window](topics/05-sliding-window/README.md) | Fixed and variable windows; longest unique substring |
| 6 | [Sorting, intervals, and cyclic sort](topics/06-sorting-intervals-cyclic-sort/README.md) | Merge intervals and bounded-value placement |
| 7 | [Week 1 review](#weekly-reviews) | Re-solve one problem from days 2–6 without notes |
| 8 | [Linked lists](topics/07-linked-lists/README.md) | Reversal, middle node, cycle detection |
| 9 | [Stacks, queues, and monotonic structures](topics/08-stacks-queues/README.md) | Valid Parentheses, Daily Temperatures |
| 10 | [Binary search](topics/09-binary-search/README.md) | Boundaries, rotated arrays, answer-space search |
| 11 | [Trees and traversal](topics/10-trees/README.md) | DFS, BFS, tree height, path questions |
| 12 | [Tries](topics/11-tries/README.md) | Prefix lookup and word search |
| 13 | [Heaps and priority queues](topics/12-heaps/README.md) | Top K, merge K lists, running median |
| 14 | [Week 2 review](#weekly-reviews) | Re-solve one list, stack, search, tree, or heap problem |
| 15 | [Graph traversal: BFS and DFS](topics/13-graph-traversal/README.md) | Connected components, grids, visited sets |
| 16 | [Topological sorting](topics/14-topological-sort/README.md) | Course Schedule and dependency ordering |
| 17 | [Shortest paths](topics/15-shortest-paths/README.md) | Choose BFS, Dijkstra, or Bellman–Ford by edge weights |
| 18 | [Union-Find](topics/16-union-find/README.md) | Connectivity and cycle detection |
| 19 | [Backtracking](topics/17-backtracking/README.md) | Subsets, permutations, combination search |
| 20 | [Greedy algorithms](topics/18-greedy/README.md) | Intervals, reachability, local-choice reasoning |
| 21 | [Week 3 review](#weekly-reviews) | Solve a graph problem and a search problem without notes |
| 22 | [Dynamic programming](topics/19-dynamic-programming/README.md) | State, recurrence, base cases, and memoization |
| 23 | [Matrices and grids](topics/20-matrices-and-grids/README.md) | Traversal, boundaries, and grid BFS/DFS |
| 24 | Mixed arrays and strings | Pick three unseen easy/medium problems; name the pattern first |
| 25 | Mixed lists, stacks, and trees | Practise pointer updates and recursive/iterative traversal |
| 26 | Mixed graphs and heaps | Select the right traversal or priority queue and explain why |
| 27 | Mixed backtracking and DP | Compare exhaustive search with reuse of overlapping states |
| 28 | Timed practice | Two medium problems, up to 45 minutes each; review mistakes |
| 29 | Mock interview | Explain, code, test, and analyze two problems without looking up hints |
| 30 | Final review and next steps | Re-solve missed problems; build a personal spaced-repetition list |

## Topic guides

Each topic guide uses plain language and answers: **what it is, who benefits, when to use it, why it helps, how to recognize and apply it, complexity, common mistakes, and what to practise**.

1. [Complexity and problem-solving basics](topics/01-complexity/README.md)
2. [Arrays and hashing](topics/02-arrays-and-hashing/README.md)
3. [Prefix sums](topics/03-prefix-sums/README.md)
4. [Two pointers](topics/04-two-pointers/README.md)
5. [Sliding window](topics/05-sliding-window/README.md)
6. [Sorting, intervals, and cyclic sort](topics/06-sorting-intervals-cyclic-sort/README.md)
7. [Linked lists](topics/07-linked-lists/README.md)
8. [Stacks, queues, and monotonic structures](topics/08-stacks-queues/README.md)
9. [Binary search](topics/09-binary-search/README.md)
10. [Trees and traversal](topics/10-trees/README.md)
11. [Tries](topics/11-tries/README.md)
12. [Heaps and priority queues](topics/12-heaps/README.md)
13. [Graph traversal: BFS and DFS](topics/13-graph-traversal/README.md)
14. [Topological sorting](topics/14-topological-sort/README.md)
15. [Shortest paths](topics/15-shortest-paths/README.md)
16. [Union-Find](topics/16-union-find/README.md)
17. [Backtracking](topics/17-backtracking/README.md)
18. [Greedy algorithms](topics/18-greedy/README.md)
19. [Dynamic programming](topics/19-dynamic-programming/README.md)
20. [Matrices and grids](topics/20-matrices-and-grids/README.md)

## Weekly reviews

At the end of each week, revisit problems you got wrong or needed hints for. For every one, write:

- What clue points to the pattern?
- What is the key invariant or state?
- Why does the algorithm make progress or stay correct?
- What are the time and extra-space costs?
- Which edge case did I miss?

If you cannot answer those questions, repeat the topic before moving on. A slower, understood solution is more valuable than a fast copied one.

## Measuring progress

By the end of the month, aim to:

- Recognize the common patterns in this folder and explain why they fit.
- Produce a correct straightforward solution before optimizing.
- State and defend time and space complexity.
- Test empty, smallest, largest, duplicate, sorted, and boundary inputs where relevant.
- Complete a medium problem in roughly 30–45 minutes **with a clear explanation**.
- Know what you still need to review; no 30-day plan replaces continued practice.

## Existing material in this repository

Use the [DSA track index](../README.md) as the existing chapter and problem reference. It includes arrays, hashing, pointers, linked lists, stacks, search, trees, heaps, graphs, dynamic programming, greedy, backtracking, and a top-problem collection.
