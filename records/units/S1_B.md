# S1-B — Sorting & Bounds

Status: In Progress — Part F Attempt 1 FAIL; remediation required
Started: 2026-09-21
Priority: Core
Assessment Status: Part E E-1 PASS; Part F F-1 FAIL — Complexity
Confidence: Provisional for S1-B
Next formal step: targeted remediation, then fresh Part F reassessment

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


## Part F — Final Assessment

### F-1 — Snapshot Ranking Queries
Status: Pending
Source: GPT-generated
Validation Tier: B
Validation Evidence:
- complete C++17 reference solution compiled and matched the sample;
- independent brute-force oracle cross-check matched 10,000 randomized valid cases;
- explicit boundary checks include N=1, exact-record equality, absent score/penalty groups, extreme ranking positions, and k=1/N.
Difficulty: R3/I2
Calibration: Comparative / Provisional
Calibration Registry: CP_Calibration_Anchor_Registry v1.1
Mode: Independent formal assessment; compiler/run allowed; standard C++ syntax/API lookup only.
Hints: None provided.

Problem statement:
N fixed participants are given. Each participant has three integers (s,p,id). Ranking is:
1. larger s first;
2. if s ties, smaller p first;
3. if both tie, smaller id first.

The original N-participant set never changes. Each query is independent; hypothetical participants are not inserted permanently.

Queries:
- 1 s p id: output 1 + the number of existing participants strictly ahead of the hypothetical participant.
- 2 s p id: output the number of existing participants ahead of the hypothetical participant or exactly equal to its full (s,p,id) triple.
- 3 s p: output the number of existing participants with exactly this s and p, ignoring id.
- 4 k: output the id of the existing participant ranked exactly k-th.

Submission requirements:
- complete C++17 program;
- must handle N,Q <= 200000;
- explain total time and space complexity in N,Q;
- explain why the ranking and all four query outputs are correct;
- explain numeric safety;
- provide at least three edge cases;
- submit measured T_solve;
- no solution/editorial search, other-person/AI help, hints, or approach review during the attempt; standard C++ syntax/API lookup is allowed.

Constraints:
- 1 <= N,Q <= 200000
- -1000000000 <= s <= 1000000000
- 0 <= p <= 1000000000
- 1 <= id <= 1000000000
- initial ids are pairwise distinct
- Type 1/2 query id may equal an existing id or be absent
- Type 4 has 1 <= k <= N

Sample:
```text
7 8
100 30 5
100 20 9
100 20 3
90 10 8
100 20 7
90 5 4
80 50 1
1 100 20 8
2 100 20 7
3 100 20
4 5
1 95 0 2
2 90 5 4
3 70 1
4 7
```

Output:
```text
3
2
3
4
5
5
0
1
```

Assessment remains active until the learner submits a final answer or explicitly gives up. No solution-relevant calibration rationale is recorded while the attempt is active.


### F-1 Attempt 1 — Learner Submission and Judgment
- Date: 2026-09-29
- Result: FAIL
- Failure Attribution: Complexity
- T_solve: 1:21:00
- Hints: None
- First-pass functional correctness: Yes on tested cases
- Assessment independence: Valid
- Capability impact: S1-B remains L3 / Provisional
- Review Debt: Open / Medium — iterator category cost and static sorted-vector vs dynamic ordered-container selection
- Part G: Locked until a fresh Part F reassessment passes or conditionally passes

Validation:
- C++17 compile succeeded; only an unused local `pair<int,int> p` warning.
- sample output matched.
- 3,000 randomized differential tests against an independent ranking oracle matched.
- Therefore the submitted program is functionally correct on the tested semantics.

Positive evidence:
- the custom comparator implements the required compound ranking: score descending, penalty ascending, id ascending;
- Type 1 and Type 2 use the correct lower/upper ordering boundaries;
- Type 3 correctly brackets all allowed ids with id=1 and id=1e9;
- Type 4 returns the k-th element under the multiset ordering;
- input integers and all produced counts fit in `int` under the stated constraints.

Blocking issue:
- `multiset` iterators are bidirectional, not random-access;
- `S.lower_bound` and `S.upper_bound` are O(log N), but `distance(S.begin(), it)` is O(N) in the worst case;
- Type 3 also uses `distance` over a multiset iterator range and can be O(N);
- Type 4 advances from `begin()` k times, so it is O(k), hence O(N) worst-case;
- therefore the query phase is O(QN) worst-case and total time is O(N log N + QN), not O((N+Q) log N);
- this violates the maximum-constraint requirement and triggers FAIL under the formal rubric.

Complexity notes:
- the insertion phase may be written as O(sum_{i=1}^N log i)=O(log(N!))=Theta(N log N);
- comparator arguments are copied by value, but each vector has exactly three integers, so this is a constant-factor inefficiency rather than a different asymptotic bound;
- space remains O(N).

Explanation issue:
- the alternative statement for Type 3, `distance(lower_bound(v1), lower_bound(v2)) + 1`, is not generally equivalent to the implemented `distance(lower_bound(v1), upper_bound(v2))`. The implemented version is correct under the stated id bounds; the alternative formula is not reliable.

Next action:
- convert F-1 to learning mode;
- remediate iterator-category operation cost and container selection for a static ranked sequence;
- reassess the same learning objectives with a fresh, non-reused Part F problem.
