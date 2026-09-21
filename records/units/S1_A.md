# S1-A — Associative Containers

> Status: Complete — Parts A-H  
> Assessment evidence through: 2026-09-18  
> Part H closure/backfill recorded: 2026-09-21  
> Priority: Core  
> Capability: L4 — Selection  
> Confidence: Provisional  
> Evidence Context: Immediate  
> Review Debt: Open — Medium  
> Immediate progression gate: Satisfied  
> Next Learning Unit: S1-B — Sorting & Bounds

## 1. Scope and Core Decision Boundaries

S1-A covered frequency/state maintenance with associative containers, ordered vs unordered selection, duplicate preservation, dynamic ordered queries, iterator safety, string-key payload complexity, forward/reverse indices, and predecessor/successor queries.

Core boundaries:
- key-only membership vs key→value state
- ordered vs unordered
- unique vs duplicate-preserving
- dynamic update vs static sorted sequence
- one-direction index vs forward/reverse indices
- ordered boundary lookup vs linear scan

## 2. Formal Assessment History

| ID | Problem | Tier | Result | T_solve | Main evidence / failure |
|---|---|---:|---|---:|---|
| E-1 | Ticker Directory | B | PASS | 19:51 | map for ordered key→value dynamic state |
| F-1 | Live Value Pool | B | PASS | 12:32 | multiset; duplicate preservation; one-occurrence erase; min/max |
| G-0 | Symbol Balance Queries | B | FAIL — Complexity | 8:11 | string payload L omitted from hash complexity |
| G-1 | AtCoder ABC073 C — Write and Erase | A | VOID | 3:55 | submitted while formal-assessment pause was active |
| G-2 | First Appearance Log | B | FAIL — Complexity | 14:02 | worst-case unordered complexity omitted |
| G-3 | AtCoder ABC235 C — The Kth Time Query | A | FAIL — Correctness | 24:09 | 1-based indexing, end() validity, vector copy |
| G-4 | Active ID Registry | B | PASS | 17:08 | expected/worst-case unordered complexity transferred cleanly |
| G-5 | AtCoder ABC298 C — Cards Query Problem | A | FAIL — Complexity | 31:27 | reverse index missing; map::value_type/reference-copy issue |
| G-6 | AtCoder ABC253 C — Max - Min Query | A | FAIL — Complexity | 8:50 | ordered-container operation costs/global accounting |
| G-7 | AtCoder ABC217 D — Cutting Woods | A | FAIL — Complexity | 22:31 | set selected, but type-2 query linearly scanned instead of lower_bound |
| G-8 | AtCoder ABC241 D — Sequence Query | A | PASS | 19:12 | multiset + lower/upper_bound + boundary-safe predecessor/successor; O(Q log Q) |

No formal passing attempt used a solution hint. Learning-mode remediation is not counted as independent mastery evidence.

## 3. Remediation History

### Complexity / string payload
After Symbol Balance Queries and First Appearance Log, the learner reviewed:
- total string payload L rather than only N/M
- expected vs worst-case unordered-container complexity
- operator[] state/space side effects

Fresh formal evidence: Active ID Registry PASS.

### Iterator validity / indexing / copying
After ABC235 C:
- check find result against end() before dereference
- store/output 1-based positions when required
- avoid copying mapped vectors; use const reference/direct access

Checkpoint passed in learning mode.

### Forward / reverse index and map::value_type
After ABC298 C:
- identify reverse-query full-scan bottleneck
- maintain reverse index when both directions are queried
- use ordered unique set where appropriate
- prefer const auto& / structured binding to avoid pair<const K,V> mismatch and hidden copies

Checkpoint passed in learning mode; still needs fresh independent formal transfer.

### Ordered-container operation cost / global accounting
After ABC253 C:
- find(x) = O(log |S|)
- erase(iterator) = amortized O(1)
- begin()/prev(end()) access = O(1)
- total successful removals are bounded by total insertions

Checkpoint passed in learning mode.

### Predecessor / successor
ABC217 D failed because the code scanned the set despite selecting the correct family. Remediation reviewed:
- lower_bound(x): first element >= x
- upper_bound(x): first element > x
- predecessor via prev(lower_bound(x)) when iterator != begin()
- dereference only when iterator != end()

Fresh independent transfer: ABC241 D PASS.

## 4. Part H — Mastery Record

### Results
- Intermediate assessment: PASS
- Final assessment: PASS
- Mandatory Part G: completed
- GPT-generated fresh reassessment: PASS — Active ID Registry
- Final External CP reassessment: PASS — AtCoder ABC241 D
- Capability: L4 — Selection
- Confidence: Provisional
- Evidence Context: Immediate
- Review Debt: Open — Medium
- Immediate Retest Needed: No
- Progression Gate: Satisfied

### Core Decision Boundary Coverage
- key→value + ordering: Immediate PASS
- duplicate-preserving ordered dynamic state: Immediate PASS
- unordered membership/state + expected/worst-case complexity: Immediate PASS
- predecessor/successor ordered boundary query: Immediate PASS after remediation and fresh transfer
- forward/reverse index: remediation PASS only; not yet independently confirmed
- static sorted sequence vs dynamic ordered container: not yet independently confirmed

Overall Coverage: **Sufficient for Provisional**.  
It is not Complete for Confirmed because delayed/mixed evidence and full Core Decision Boundary coverage are still missing.

### Review Debt
Severity remains **Medium**. Earlier repeated formal failures show that selecting an associative-container family does not always imply correct use of its asymptotically essential operations. ABC241 D is clean independent recovery evidence, so the debt is non-blocking, but it remains open until delayed/mixed transfer is clean.

## 5. Next Action

Proceed to **S1-B — Sorting & Bounds**.  
Later, in a delayed/mixed assessment with the topic hidden, re-check:
1. ordered associative-container boundary operations,
2. complexity accounting,
3. reverse-index recognition,
4. static sorted sequence vs dynamic ordered container choice.

## 6. Preservation / Provenance

The synchronized learner-facing source is:
- `textbook_by_GPT/Main_Contents/S1_A_Learning_Unit.tex`

Assessment index:
- `textbook_by_GPT/Appendix/S1_A_exam.tex`

Reusable reference:
- `textbook_by_GPT/Appendix/S1_A_Appendix.tex`

The exact learner code and detailed problem blocks already preserved in the synchronized textbook remain authoritative alongside recoverable original dialogue. This Unit Source Record is a backfill of the previously missing persistent unit-level record and does not erase or replace prior FAIL/VOID evidence.
