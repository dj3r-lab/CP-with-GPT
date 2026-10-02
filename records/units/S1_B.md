# S1-B — Sorting & Bounds

Status: Complete — Parts A-H complete; Immediate Progression Gate satisfied; review block active
Started: 2026-09-21
Priority: Core
Assessment Status: Part E E-1 PASS; Part F F-1 FAIL — Complexity; F-2 PASS; Part G G-A FAIL — Implementation; G-B FAIL — Correctness
Confidence: Provisional for S1-B
Next formal step: review block before the next Core Learning Unit; fresh non-reused reassessments for G-A/G-B debt objectives

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


## Targeted Remediation — Iterator Cost and Static/Dynamic Container Choice (2026-09-30)

Mode: Learning mode — not formal mastery evidence.

### Remediation objective
Part F F-1 failed on complexity despite functionally correct ranking semantics. The blocking issue is separated into two linked decisions:

1. distinguish the complexity of a tree search from the complexity of moving between iterators;
2. choose a sorted random-access sequence rather than a dynamic ordered tree when the dataset is fixed and the queries require rank/count/k-th access.

### 1. Search cost is not the whole query cost
For `std::multiset`:
- `S.lower_bound(x)` / `S.upper_bound(x)`: O(log N);
- iterator category: bidirectional;
- `std::distance(S.begin(), it)`: O(N) worst case;
- advancing k positions from `begin()`: O(k), hence O(N) worst case.

Therefore a query such as
```cpp
auto it = S.lower_bound(x);
auto rank = distance(S.begin(), it);
```
is O(log N + N) = O(N), not O(log N).

The tree stores enough structure to navigate by key, but a standard `set`/`multiset` does not maintain subtree sizes that would reveal the numeric rank of a node. Finding a key boundary and finding its 0-based/1-based rank are different operations.

### 2. Why sorted vector fits a static ranked snapshot
If the N records never change:
- build a `vector<Record>`;
- sort once using the ranking comparator: O(N log N);
- `lower_bound` / `upper_bound` on the vector: O(log N);
- iterator subtraction `it - v.begin()`: O(1), because vector iterators are random-access;
- k-th ranked record `v[k-1]`: O(1).

Thus rank/count boundary queries can remain O(log N), and direct k-th access is O(1).

### 3. Static vs dynamic selection rule
Prefer a sorted `vector` when:
- the dataset is fixed or updated only in occasional batches;
- there are many lookup/rank/range-count queries;
- direct index/k-th access matters;
- one O(N log N) preprocessing sort is acceptable.

Prefer `set`/`multiset` when:
- insert/erase operations occur continually between queries;
- ordered membership, predecessor/successor, min/max, or key boundaries are needed;
- numeric rank or arbitrary k-th order statistic is not required.

A standard `set`/`multiset` is not an order-statistics tree. If both frequent dynamic updates and rank/k-th queries are required, later tools such as coordinate compression + Fenwick/segment tree or an order-statistics tree may be appropriate; those are outside the current S1-B requirement.

### 4. Complexity accounting checklist
For every ordered-query solution, analyze the entire expression rather than only the named STL operation:

1. What container supplies the iterator?
2. What iterator category does it provide?
3. What is the cost of the search operation?
4. What happens after the search — subtraction, `distance`, `advance`, traversal, output?
5. Is the dataset static, batch-updated, or dynamically updated?
6. Does the query require only a boundary by key, or also a numeric rank/k-th element?

Example:
`multiset::lower_bound` O(log N) + `distance(begin,it)` O(N) => O(N).

By contrast:
vector `lower_bound` O(log N) + iterator subtraction O(1) => O(log N).

### 5. Transfer back to F-1
F-1's participant set is explicitly fixed. The required queries ask for insertion rank, prefix/equality counts, a same-(s,p) count, and the k-th ranked participant. These requirements strongly favor a once-sorted random-access sequence. The earlier `multiset` solution got the ordering semantics right but chose dynamic-update capability that the problem never needed, while losing efficient rank/k-th access.

This remediation does not change F-1's FAIL result and is not mastery evidence. F-1 remains learning-only. The next formal evidence must come from a fresh Part F problem.

## Part F Fresh Reassessment — F-2 Archived Job Queries
Status: Pending / Active
Source: GPT-generated
Validation Tier: B
Validation Evidence:
- complete C++17 reference solution compiled successfully;
- sample output matched;
- 5,000 randomized valid cases matched an independent brute-force oracle;
- explicit edge coverage includes N=1, absent day/priority groups, interval boundaries not present in the data, equal interval endpoints, duplicate (d,p) groups with distinct ids, and extreme allowed values.
Difficulty: R3/I2
Calibration: Comparative / Provisional
Calibration Registry: CP_Calibration_Anchor_Registry v1.1
Calibration rationale:
- the implementation remains a standard sorting/bounds composition consistent with I2;
- recognition requires choosing a static random-access ordered representation and applying the same compound ordering consistently across multiple boundary shapes, placing it above a direct R2/I2 bounds exercise and near the Registry's R3/I2 selection anchors;
- F-1's exposed surface and query set are not reused.
Mode: Independent formal assessment; compiler/run allowed; standard C++ syntax/API lookup only.
Hints: None provided.

Problem statement:
N archived jobs are fixed. Each job has (d,p,id). Global order is d ascending, then p descending, then id ascending.

Queries:
- 1 d1 p1 id1 d2 p2 id2: count existing records in the inclusive full-order interval [A,B]. A and B need not exist; A is guaranteed not to come after B.
- 2 d p: count records with exact d and priority strictly greater than p.
- 3 d p: count records with exact d and exact p, ignoring id.
- 4 L R: count records with L <= d <= R.

Submission requirements:
- complete C++17 program;
- handle N,Q <= 200000;
- explain total time and space complexity in N,Q;
- explain why the global order and all four queries are correct;
- explain numeric safety;
- provide at least three edge cases;
- submit measured T_solve;
- no solution/editorial search, other-person/AI help, hints, or approach review; standard C++ syntax/API lookup only.

Constraints:
- 1 <= N,Q <= 200000
- -1e9 <= d <= 1e9
- 0 <= p <= 1e9
- 1 <= id <= 1e9
- initial ids are pairwise distinct
- Type 1 boundaries obey the same d,p,id ranges and A <= B under the defined order
- Type 4: -1e9 <= L <= R <= 1e9

Sample Input:
```text
8 8
1 90 5
1 70 3
1 70 8
2 100 2
2 40 6
3 80 4
3 80 1
5 50 7
1 1 70 4 3 80 1
2 1 70
3 1 70
4 2 3
1 0 0 1 1 70 8
2 3 80
3 4 10
4 1 5
```

Sample Output:
```text
4
1
2
4
3
0
0
8
```

Assessment remains active until the learner submits a final answer or explicitly gives up. No solution-relevant calibration rationale will be surfaced during the active attempt.


### F-2 Attempt 1 — Learner Submission and Judgment
- Date: 2026-09-30
- Result: PASS
- Validation Tier: B
- Difficulty: R3/I2 (Comparative / Provisional)
- T_solve: 2:40:00
- Hints: None
- Assessment independence: Valid
- Capability impact: S1-B advances to L4 / Provisional
- Review Debt: S1-B Medium debt Resolved
- Part G: Unlocked

Validation:
- submitted C++17 compiled successfully;
- sample output matched;
- all three learner-provided edge cases matched;
- 3,000 randomized differential cases matched an independent brute-force oracle.

Correctness:
- comparator implements d ascending, p descending, id ascending and is strict because equal records return false;
- Type 1 lower_bound(A) and upper_bound(B) correctly count the inclusive full-order interval [A,B];
- Type 2 uses the earliest possible tuple for day d and the earliest tuple at priority p, so the half-open interval counts exactly same-d records with priority strictly greater than p;
- Type 3 brackets the whole id range [1,1e9] at fixed (d,p), so it counts exactly the matching group;
- Type 4 brackets the first possible record at day L and the last possible record at day R, so it counts all records with L<=d<=R.

Complexity:
- building the fixed-size three-int records is O(N);
- sorting is O(N log N);
- each lower_bound/upper_bound is O(log N);
- vector iterators are random-access, so distance between returned iterators is O(1);
- total time is O(N log N + Q log N)=O((N+Q) log N);
- total stored data is O(N).

Numeric safety:
- d, p, id and all sentinel values are within signed 32-bit int;
- no arithmetic on those values can overflow in the submitted code;
- each count/distance magnitude is at most N<=200000, so conversion to int is safe.

Non-blocking explanation issue:
- the Query 2 prose is imprecise when it says the interval extends to the 'last' record; the code actually stops at the first record of priority p and therefore correctly excludes priority == p. The intended strict-greater boundary is nevertheless implemented correctly and the surrounding explanation identifies the two relevant boundaries.

Edge cases submitted:
1. N=1, (1,2,3), query 2 1 1 -> 1
2. N=1, (1,2,3), query 3 1 2 -> 1
3. N=1, (1,2,3), query 4 -1 100 -> 1

Interpretation:
- F-1's historical FAIL remains valid and is preserved.
- F-2 independently demonstrates the remediated static sorted-vector vs dynamic ordered-container selection and iterator-cost accounting.
- S1-B Immediate Progression Gate is satisfied through Part E PASS + fresh Part F PASS, with Capability L4 / Confidence Provisional.
- Part G is now required.


## Part G — Adaptive Extra Problems

### G-A — Trade Archive Queries
Status: Pending / Active
Source: GPT-generated
Validation Tier: B
Validation Evidence:
- complete C++17 reference solution compiled and matched the sample;
- 5,000 randomized valid cases matched an independent brute-force oracle;
- grouped-empty, duplicate-time, missing-symbol, k-too-large, and global-boundary cases were included.
Difficulty: R3/I2 (Comparative / Provisional)
Calibration Registry: CP_Calibration_Anchor_Registry v1.1
Mode: Independent formal assessment; no hints.

Problem statement:
N historical trades are fixed. Each trade has an integer symbol id s and integer timestamp t. Duplicate timestamps are allowed, including within the same symbol. The archive never changes after input.

Queries:
- 1 s L R: count trades of symbol s with L <= t <= R.
- 2 s x: count trades of symbol s with t < x.
- 3 s k: output the k-th smallest timestamp among trades of symbol s; output NONE if fewer than k exist.
- 4 L R: count all trades, regardless of symbol, with L <= t <= R.

Submission requirements:
- complete C++17 program;
- handle N,Q <= 200000;
- explain total time and space complexity in N,Q;
- explain why all four query types are correct;
- explain numeric safety;
- provide at least three edge cases;
- submit measured T_solve;
- no solution/editorial search, other-person/AI help, hints, or approach review; standard C++ syntax/API lookup only.

Constraints:
- 1 <= N,Q <= 200000
- 1 <= s <= 1000000000
- -1000000000 <= t,x,L,R <= 1000000000
- L <= R
- Type 3 has 1 <= k <= N

Sample Input:
```text
8 8
10 5
20 3
10 2
10 5
30 9
20 8
10 -1
30 4
1 10 2 5
2 20 8
3 10 3
4 4 8
1 40 -100 100
3 30 3
2 10 -1
4 10 20
```

Sample Output:
```text
3
1
5
4
0
NONE
0
0
```

### G-B — AtCoder ABC308 C — Standings
Status: Pending / Active
Source: External CP — official AtCoder
Validation Tier: A
Mode: Independent formal assessment; no hints.

Problem statement:
N people are numbered 1..N. Person i has Ai successful outcomes and Bi unsuccessful outcomes. Their success rate is Ai/(Ai+Bi). Output all person numbers in descending order of success rate; if rates are equal, output smaller person numbers first.

Submission requirements:
- solve the official AtCoder ABC308 C problem independently in C++17;
- submit the complete code here, and include the AtCoder verdict if you submit on the platform;
- explain time and space complexity;
- explain numeric safety;
- provide at least two edge cases;
- submit measured T_solve;
- no editorial/solution search, other-person/AI help, hints, or approach review; standard C++ syntax/API lookup only.

Constraints:
- 2 <= N <= 200000
- 0 <= Ai,Bi <= 1000000000
- Ai+Bi >= 1

Official problem: https://atcoder.jp/contests/abc308/tasks/abc308_c

Both G-A and G-B must be completed. Results are independent evidence; an Extra FAIL does not retroactively cancel the Part F PASS.

### G-A Attempt 1 — Learner Submission and Judgment
- Date: 2026-10-02
- Result: FAIL
- Failure Attribution: Implementation
- Validation Tier: B
- Difficulty: R3/I2 (Comparative / Provisional)
- T_solve: 1:09:21 total; code complete at 50:26
- Hints: None
- Assessment independence: Valid
- Capability impact: S1-B remains L4 / Provisional
- Review Debt: Open / Low — k-th lookup iterator-boundary safety
- Progression impact: none; Part F PASS / immediate progression gate remain valid
- G-B: still pending

Validation:
- submitted C++17 code compiled;
- sample output matched;
- Query 1, 2, and 4 boundary constructions are correct;
- a valid counterexample N=1, trade=(1,5), query `3 2 2` makes lower_bound return end(), then `it1 += 1` advances past end();
- libstdc++ debug iterators abort on this invalid advance; normal release behavior is undefined.

Blocking issue:
- Type 3 executes `it1 += k - 1` before proving that at least k records of symbol s exist;
- checking `it1 != arc1.end()` afterward is too late;
- the same defect occurs when symbol s exists but has fewer than k records and the advance crosses vector end().

Positive evidence:
- two static sorted vectors are a valid solution family;
- cmp1 correctly orders by symbol then timestamp;
- cmp2 correctly orders by timestamp then symbol;
- Query 1, 2, and 4 are correct;
- intended total time O((N+Q)logN) and space O(N) are correct;
- numeric-safety reasoning is correct.

Next action:
- G-A becomes learning-only and cannot be reused;
- open Low Review Debt for grouped k-th lookup boundary safety;
- complete G-B External CP Extra;
- later reassess with a fresh non-reused Extra.


### G-B Attempt 1 — Learner Submission and Judgment
- Date: 2026-10-02
- Problem: AtCoder ABC308 C — Standings
- Result: FAIL
- Failure Attribution: Correctness
- Validation Tier: A
- T_solve: 20:30 total; code complete at 14:26
- Hints: None
- Assessment independence: Valid
- Capability impact: S1-B remains L4 / Provisional
- Review Debt: Open / Medium — exact rational comparison / floating-point precision in comparator
- Progression impact: none; Part F PASS / immediate progression gate remain valid

Validation:
- submitted C++17 code compiled;
- official sample 1 matched;
- official sample 3 failed: expected `3 1 4 2`, submitted program produced `1 3 4 2`;
- success rate was stored in `float` before being placed in `pair<double,int>`.

Blocking issue:
- distinct exact rational success rates can round to the same binary32 value;
- official sample 3 therefore creates false ties and the index tie-break yields the wrong order;
- converting the already-rounded value to double does not recover precision.

Positive evidence:
- sorting + custom comparator is the correct algorithmic family;
- tie-breaking by smaller person number is structurally correct;
- O(N log N) time and O(N) space are correct.

Remediation target:
- compare the rational rates exactly with integer cross-products; required products fit signed 64-bit under the stated constraints.

Reuse:
- G-B becomes learning-only after FAIL and cannot be reused for formal reassessment.

## Part H — S1-B Immediate Closure
- Part E: PASS — Static Score Queries
- Part F: PASS on fresh reassessment F-2 — Archived Job Queries
- Part G: G-A FAIL — Implementation; G-B FAIL — Correctness
- Capability: L4 — Selection
- Confidence: Provisional
- Immediate Progression Gate: Satisfied
- Review Debt: Low (grouped k-th iterator safety) + Medium (exact rational comparator / floating-point precision)
- Unit Coverage: Parts A-H complete
- Next action: review block before another Core Learning Unit because unresolved Core Review Debt exceeds the operating threshold.


## Review Block 1 — Precision and Boundary Safety Remediation (2026-10-02)
Mode: Learning mode — not formal mastery evidence.

Targets:
1. Medium Review Debt: exact rational comparison / floating-point precision in comparator.
2. Low Review Debt: grouped k-th lookup iterator-boundary safety.

### A. Exact rational ordering
- When an ordering criterion is mathematically exact, storing a ratio as float/double can merge distinct values and create false ties.
- For positive denominators, compare ratios by integer cross multiplication rather than by a rounded floating key.
- In the ABC308-C form Ai/(Ai+Bi) vs Aj/(Aj+Bj), cancellation reduces the comparison to Ai*Bj vs Aj*Bi.
- With Ai,Bi <= 1e9, each simplified product is <= 1e18 and fits signed 64-bit long long.
- If values are stored as int, cast before multiplication (e.g. 1LL * Ai * Bj) so the multiplication itself occurs in 64-bit arithmetic.
- Exact equality of cross-products is the only point at which the secondary id/index tie-break should be used.

### B. Safe grouped k-th lookup
- Random-access means O(1) positional movement, not permission to move outside [begin,end].
- `end()+positive` or any iterator arithmetic whose result is beyond one-past-end is undefined behavior.
- For a group ordered contiguously in a vector, first compute its half-open range [first,last), then count = last-first.
- Only if count >= k may `first + (k-1)` be formed. This proves the target lies strictly before last and therefore within the vector.
- Checking `it != end()` after an unchecked jump is too late because undefined behavior may already have occurred.

### Transfer rule
Before writing a comparator or k-th query, identify the exact invariant to preserve:
- comparator: is approximate numerical equality acceptable, or must mathematical order be exact?
- iterator arithmetic: what fact proves the destination iterator is within the valid range before the movement occurs?

This remediation does not resolve either debt by itself. Both require fresh independent formal evidence.


## Review Block 1 — Fresh Reassessment (2026-10-02)
Status: Active / Pending
Purpose: independently reassess the two S1-B debts opened by G-A and G-B after learning-mode remediation.
Assessment integrity: no hints or approach review; any such request makes the corresponding attempt non-passing.

### RB-1 — Batch Priority Board
Status: PASS
Source: GPT-generated
Validation Tier: B
Difficulty: R2/I2 (Comparative / Provisional)
Validation evidence: C++17 reference compile/sample + 5,000 randomized comparisons against an exact rational oracle.
Target debt: Medium — exact rational comparison / floating-point precision.

Problem: N batches are indexed 1..N. Batch i has integers (a_i,b_i,c_i) and exact priority score (a_i+b_i)/c_i. Output indices in descending exact score; ties use smaller index first.

Submission conditions: complete C++17 program; N<=200000; total O(N log N) or better; explain correctness, time/space complexity, numeric safety; at least two edge cases; measured T_solve; no hints/editorials/other-person-or-AI help/approach review; standard C++ syntax/API lookup only.

Constraints: 0<=a_i,b_i<=1e9; 1<=c_i<=1e9.

Sample Input:
```text
5
3 1 2
1 1 1
2 1 3
9 0 3
0 5 5
```
Sample Output:
```text
4 1 2 3 5
```

### RB-2 — Locker Archive Queries
Status: PASS
Source: GPT-generated
Validation Tier: B
Difficulty: R2/I2 (Comparative / Provisional)
Validation evidence: C++17 reference compile/sample + 5,000 randomized comparisons against an independent grouped brute-force oracle.
Target debt: Low — grouped k-th lookup iterator-boundary safety.

Problem: N fixed records have group g and integer value x; duplicates are allowed. Query 1 g k outputs the k-th smallest x in group g, or NONE if the group has fewer than k records. Query 2 g L R outputs the number of records in group g with L<=x<=R.

Submission conditions: complete C++17 program; N,Q<=200000; worst-case total O((N+Q) log N) or better; explain correctness, time/space complexity, numeric safety; at least three edge cases; measured T_solve; no hints/editorials/other-person-or-AI help/approach review; standard C++ syntax/API lookup only.

Constraints: 1<=g<=1e9; -1e9<=x,L,R<=1e9; 1<=k<=N; L<=R.

Sample Input:
```text
7 6
10 5
20 3
10 2
10 5
30 9
20 8
30 4
1 10 3
1 10 4
2 20 1 7
1 40 1
2 30 4 9
1 20 2
```
Sample Output:
```text
5
NONE
1
NONE
2
8
```

Both reassessments are independent. Passing one resolves only its corresponding debt; the other debt remains open until independently passed.


### RB-1 Attempt 1 — Learner Submission and Judgment
- Date: 2026-10-02
- Result: PASS
- Validation Tier: B
- Difficulty: R2/I2 (Comparative / Provisional)
- T_solve: 22:10 total; code complete at 10:51
- Hints: None
- Assessment independence: Valid
- Target debt: Medium — exact rational comparison / floating-point precision
- Debt result: Resolved

Validation:
- learner C++17 code compiled;
- sample output matched;
- 5,000 randomized exact-rational differential cases matched an independent oracle.

Correctness evidence:
- comparator compares (a_i+b_i)/c_i and (a_j+b_j)/c_j by exact cross multiplication;
- all c values are positive, so multiplying by c_i*c_j preserves the inequality direction;
- exact equality of cross-products falls through to smaller batch index, implementing the required tie-break;
- equal records return false in both comparator directions except for the index tie-break, so the ordering is strict.

Complexity / numeric safety:
- construction O(N), sorting O(N log N), output O(N), total O(N log N);
- storage O(N);
- (a+b)c is at most 2*10^18, within signed 64-bit long long;
- because a,b,c are already long long, both addition and multiplication occur in 64-bit arithmetic.

Non-blocking documentation issue:
- both submitted edge-case labels say N=1 although the first lists two records and the second lists three records; the intended N values are 2 and 3. This does not affect program correctness or the assessed objective.

State impact:
- G-B Medium exact-rational / floating-point Review Debt is Resolved;
- S1-B remains L4 / Provisional;
- RB-2 Low iterator-boundary debt remains Open;
- global unresolved Core Review Debt decreases from 6 to 5, so the review block remains active because the count is still above 4.


### RB-2 Attempt 1 — Learner Submission and Judgment
- Date: 2026-10-02
- Result: PASS
- Validation Tier: B
- Difficulty: R2/I2 (Comparative / Provisional)
- T_solve: 27:10 total; code complete at 15:01
- Hints: None
- Assessment independence: Valid
- Target debt: Low — grouped k-th lookup iterator-boundary safety
- Debt result: Resolved

Learner code:
```cpp
#include <iostream>
#include <string>
#include <algorithm>
#include <vector>
#include <map>
#include <unordered_map>
#include <set>
#include <unordered_set>
#include<iterator>

using namespace std;

bool cmp(const vector<int>& v1, const vector<int>& v2) {
    if (v1[0] != v2[0]) {
        return v1[0] < v2[0];
    }
    else {
        return v1[1] < v2[1];
    }
}

int main()
{
    int N = 0;
    cin >> N;
    int Q = 0;
    cin >> Q;
    vector<vector<int>> V = {};
    for (int i = 0; i < N; i += 1) {
        int g, x;
        cin >> g >> x;
        vector<int> v = { g, x };
        V.push_back(v);
    }
    sort(V.begin(), V.end(), cmp);

    for (int i = 0; i < Q; i += 1) {
        int q = 0;
        cin >> q;
        if (q == 1) {
            int g, k;
            cin >> g >> k;

            vector<int> v1 = { g, -1000000000 };
            vector<int> v2 = { g, 1000000000 };
            auto it1 = lower_bound(V.begin(), V.end(), v1, cmp);
            auto it2 = upper_bound(V.begin(), V.end(), v2, cmp);
            if (distance(it1, it2) < k) {
                cout << "NONE" << '\n';
            }
            else {
                it1 += k - 1;
                cout << (*it1)[1] << '\n';
            }
        }
        else {
            int g, L, R;
            cin >> g >> L >> R;

            vector<int> v1 = { g, L };
            vector<int> v2 = { g, R };
            auto it1 = lower_bound(V.begin(), V.end(), v1, cmp);
            auto it2 = upper_bound(V.begin(), V.end(), v2, cmp);
            cout << distance(it1, it2) << '\n';
        }
    }
}
```

Validation:
- learner C++17 compiled and matched the sample;
- 5,000 randomized grouped-oracle cases all matched;
- Type 1 obtains the complete group half-open range [it1,it2), checks its size before iterator arithmetic, and only then forms it1+(k-1);
- therefore the destination is guaranteed to lie inside the group and before it2 <= end();
- Type 2 lower_bound/upper_bound correctly counts the inclusive [L,R] values in group g;
- vector iterator distance is O(1), so each query remains O(log N).

Complexity / safety:
- sort O(N log N), each query O(log N), total O((N+Q)logN), storage O(N);
- all input scalars fit int; all distances/counts are <= N <= 200000, also int-safe;
- comparator is strict: equal (g,x) pairs return false in both directions.

Interpretation:
- G-A's iterator-boundary weakness is independently remediated;
- S1-B has no active immediate Review Debt;
- Review Block 1 is complete with RB-1 PASS + RB-2 PASS;
- S1-B remains L4 / Provisional pending delayed/mixed evidence;
- global unresolved Core Review Debt count is now 4, so the forced review-block trigger (>4) is cleared.
