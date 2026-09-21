# S1-B — Sorting & Bounds

Status: In Progress — Parts A-D complete
Started: 2026-09-21
Priority: Core
Assessment Status: Part E E-1 PASS
Confidence: Provisional for S1-B
Next formal step: Part F — Final Assessment

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


## Part E — Intermediate Assessment

### E-1 — Static Score Queries
Status: PASS
Source: GPT-generated
Validation Tier: B
Validation Evidence:
- complete C++17 reference solution compiled and executed;
- sample output matched;
- 200 randomized batches matched an independent brute-force oracle;
- explicit boundary checks covered N=1, absent values, duplicate values, equal L/R, and extreme allowed values.
Difficulty: R2/I2
Calibration: Comparative / Provisional
Calibration Registry: CP_Calibration_Anchor_Registry v1.1
Calibration rationale:
- recognition is a simple variation of the just-learned static sorted-bound query pattern, so it is above fully explicit R1 but below hidden-selection R3;
- implementation uses one standard preprocessing/query pattern with several strict/non-strict boundaries, consistent with I2;
- compared against the Registry, it is structurally closer to R2/I2 anchors such as Sqrt(x) than to R3/I2 selection-heavy problems.
Mode: Independent formal assessment; compiler/run allowed; C++ syntax/API lookup only.
Hints: None provided.
Result: PASS

Problem statement:
N fixed integer scores A_i are given, followed by Q queries:
- 1 x: output count of A_i < x
- 2 x: output count of A_i = x
- 3 L R: output count of L < A_i <= R

Submission requirements:
- complete C++17 program;
- handle N,Q <= 200000;
- explain total time and space complexity in N,Q;
- explain numeric safety;
- provide at least two edge cases;
- submit measured T_solve;
- no solution/editorial search, other-person/AI help, hints, or approach review during the attempt; standard C++ syntax/API lookup is allowed.

Constraints:
- 1 <= N,Q <= 200000
- -1e9 <= A_i,x,L,R <= 1e9
- L <= R

Sample:
Input
8 6
5 1 5 2 9 5 7 2
1 5
2 5
3 2 7
1 -10
2 2
3 9 9

Output
3
3
4
0
2
0

Learner submission:
- T_solve: 11:29
- Hints: None
- First-pass Correct: Yes
- Result: PASS
- Failure Attribution: N/A
- Capability evidence: L3 Implementation, with correct static sorted-vector boundary use
- Confidence: Provisional / Immediate
- Review Debt: None

Validation:
- C++17 compile succeeded; only an unused-variable warning for Type-1 up_b.
- sample matched;
- both learner edge cases matched;
- 2,000 randomized differential tests against an independent brute-force oracle matched.

Correctness:
- Type 1: lower_bound(x)-begin() counts values < x.
- Type 2: upper_bound(x)-lower_bound(x) counts values == x.
- Type 3: upper_bound(R)-upper_bound(L) counts L < value <= R.

Complexity:
- Final O(N log N + Q log N) = O((N+Q) log N) is correct.
- Non-blocking explanation error: the claim that the search examines all elements when the target is near the end is false for vector random-access lower_bound/upper_bound; the range is narrowed logarithmically.
- Stored input space is O(N). Input/query integers fit int; iterator subtraction yields vector<int>::difference_type with magnitude at most N.

Edge cases:
1. N=1, A=1234 with queries (<123), (=1234), (12,1357] -> 0,1,1.
2. N=1, A=1e9 with query (999999999,1e9] -> 1.

No retest is needed. Part F should assess remaining S1-B decision boundaries, especially custom comparator / selection.
