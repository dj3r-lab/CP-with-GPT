# S1-B — Sorting & Bounds

Status: In Progress — Parts A-D complete
Started: 2026-09-21
Priority: Core
Assessment Status: Not yet assessed
Confidence: Unverified for S1-B
Next formal step: Part E in a separate response after learner discussion/Q&A

## Scope
S1-B extends S0-B sort/comparator syntax and S1-A ordered-container bounds.

Covered:
- std::sort
- custom comparator / tie-breaking
- sorted-vector lower_bound / upper_bound
- duplicates and half-open boundaries
- equality/range counts from two bounds
- sorted vector vs set / multiset selection

Deferred to Stage 3:
- manual Binary Search implementation/invariants
- binary search on answer

## Core Decision Boundaries
1. repeated linear scan vs sort once + repeated bounds
2. sorted vector vs dynamic ordered set/multiset
3. lower_bound (first >= x) vs upper_bound (first > x)
4. default ordering vs custom comparator
5. bounds ordering must match the ordering used to sort

Immediate Coverage Floor for later assessment:
- sorted-vector boundary query
- duplicate-aware lower/upper semantics
- compound custom comparator
- vector vs ordered-container selection by update pattern

## Parts A-D Notes
Sorting is treated as preprocessing. Static or almost-static data can often change repeated O(N) scans into O(log N) boundary queries after one O(N log N) sort.

Key facts:
- sort is ascending by default.
- comparator answers whether a should come before b and must be strict.
- std::sort does not guarantee original-order preservation among comparator-equivalent elements.
- lower_bound(x): first >= x.
- upper_bound(x): first > x.
- equal-x block: [lower_bound(x), upper_bound(x)).
- count(x) = upper_bound(x) - lower_bound(x).
- count([L,R]) = upper_bound(R) - lower_bound(L).
- vector iterator minus begin gives an index; end must not be dereferenced.
- static/batch ordered queries often favor sorted vector; repeated dynamic update often favors set/multiset.
- binary_search gives existence; bounds give positions.
- do not claim a standard-guaranteed O(1) auxiliary-space bound for std::sort without an implementation/analysis assumption.

## Worked Example — Price Range Queries
N transaction prices, duplicates allowed, followed by Q inclusive intervals [L,R]. Each query returns the number of prices in the interval and the minimum such price or NONE.

Brute force: O(NQ).

Sorted solution:
- left = lower_bound(L)
- right = upper_bound(R)
- count = right - left
- if left != right, minimum = *left

Total time: O(N log N + Q log N).
Explicit stored input: O(N).

Trace:
original [8,3,5,5,10,2]
sorted [2,3,5,5,8,10]
[4,8] -> count 3, minimum 5
[6,7] -> count 0, NONE
[5,5] -> count 2, minimum 5

## Evaluation State
No S1-B formal assessment problem has been presented. Part E must be a later separate response. No S1-B PASS/FAIL, Capability, R/I calibration, or Review Debt is created by Parts A-D alone.


## Q&A — Iterator Type과 가능한 연산

The learner requested a detailed explanation of iterator types and supported operations before Part E.

Key points covered:
- iterator is a pointer-like abstraction, not necessarily a raw pointer;
- explicit types such as `vector<int>::iterator` and practical use of `auto`;
- `iterator` vs `const_iterator`, plus `cbegin/cend`;
- C++17 categories: input, output, forward, bidirectional, random-access;
- C++20 adds the contiguous iterator concept;
- container mapping: vector/array/string random-access; deque random-access; list bidirectional; forward_list forward; ordered associative containers bidirectional; unordered associative containers forward;
- operation hierarchy: dereference, increment, decrement, arithmetic, indexing, iterator subtraction/order comparison;
- `vector` iterator subtraction is valid, `set` iterator subtraction is not;
- `next/prev/advance/distance` and the fact that distance/advance may be O(N) for non-random-access iterators;
- algorithm requirements: sort needs random-access; reverse needs bidirectional traversal plus swappable elements; find works with input iterators;
- generic `std::lower_bound` on set iterators can require O(N) iterator increments even though comparisons are logarithmic; use `set::lower_bound` for O(log N);
- half-open range [begin,end), and end() must not be dereferenced;
- map iterator exposes `pair<const K,V>`: key is immutable but mapped value can be modified;
- reverse_iterator direction semantics;
- iterator invalidation patterns for vector, node-based ordered containers, and unordered-container rehash.

This was learning-mode Q&A only. No S1-B assessment was started and no PASS/FAIL/mastery evidence was created.
