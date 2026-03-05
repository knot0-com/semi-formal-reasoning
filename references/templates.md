---
title: "Semi-Formal Reasoning Templates"
status: active
date: "2026-03-05"
parents: ["semi-formal-reasoning/SKILL.md"]
tags: ["templates", "semi-formal", "reasoning"]
input: "Code diffs, test files, repository context"
output: "Structured verification certificates"
position: "Reference templates loaded on demand by SKILL.md"
---

# Semi-Formal Reasoning Templates

Three task-specific templates adapted from the Agentic Code Reasoning paper
(Ugare & Chandra, 2026). Each template enforces structured analysis that
prevents unsupported claims.

## Template 1: Patch Verification

Use when verifying if code changes are correct, or if two patches are equivalent.

```
DEFINITIONS:
D1: A patch is CORRECT iff executing the test suite produces
    all expected pass/fail outcomes after applying the patch.
D2: Two patches are EQUIVALENT MODULO TESTS iff the test suite
    produces identical pass/fail outcomes for both patches.

PREMISES (state what the patch does):
P1: Patch modifies [file(s)] by [specific change with line numbers]
P2: The change alters [function/method] behavior from [old] to [new]
P3: Relevant tests check [specific behavior]

IMPORTS AND DEPENDENCIES:
For each modified file:
  - List imports at file:line
  - Check for shadowed names (module-level, class-level, local)
  - Identify call sites that invoke modified code

ANALYSIS OF TEST BEHAVIOR:
For each relevant test:
  Test: [test_name] at [file:line]
  Purpose: [what the test verifies]

  Before patch:
    Execution trace: [function calls with file:line refs]
    Expected outcome: [PASS/FAIL]

  After patch:
    Execution trace: [function calls with file:line refs]
    Expected outcome: [PASS/FAIL]

  Comparison: [SAME/DIFFERENT] outcome

EDGE CASES:
For each edge case identified:
  Case: [description]
  Before: [behavior at file:line]
  After: [behavior at file:line]
  Impact on tests: [which tests affected, how]

COUNTEREXAMPLE (if patch is INCORRECT or patches NOT EQUIVALENT):
  Test [name] produces different/wrong outcome because:
  [specific execution trace with file:line evidence]

  OR

NO COUNTEREXAMPLE EXISTS:
  All test paths traced. No divergent outcomes found.
  [List each test and its identical outcome]

FORMAL CONCLUSION:
By D1/D2, since test outcomes are [identical/different],
the patch is [CORRECT/INCORRECT] | patches are [EQUIVALENT/NOT EQUIVALENT].
```

## Template 2: Fault Localization

Use when finding the root cause of a test failure.

```
PHASE 1 — TEST SEMANTICS ANALYSIS:
Failing test: [test_name] at [file:line]

State test behavior as formal premises:
P1: Test invokes [method/function] at [file:line]
P2: Test expects [specific outcome] based on assertion at [file:line]
P3: Test sets up state by [setup steps with file:line refs]

What the test is checking:
[Concrete description of the behavior under test]

PHASE 2 — CODE PATH TRACING:
Trace execution from test entry to production code.

Entry point: [test method] at [file:line]
  → calls [function] at [file:line]
    → calls [function] at [file:line]
      → [continue until reaching the code under test]

For each function in the chain:
  Function: [name] at [file:line]
  Parameters: [types and values from test context]
  Returns: [type and expected value]
  Side effects: [state mutations, if any]
  VERIFIED: [YES — traced implementation | NO — assumed behavior]

PHASE 3 — DIVERGENCE ANALYSIS:
Identify where implementation diverges from test expectations.

Hypothesis 1: [description]
  Evidence for: [file:line observations]
  Evidence against: [file:line observations]
  Verdict: [SUPPORTED/REJECTED]

Hypothesis 2: [description]
  Evidence for: [file:line observations]
  Evidence against: [file:line observations]
  Verdict: [SUPPORTED/REJECTED]

[Continue until root cause identified]

PHASE 4 — RANKED PREDICTIONS:
Ranked by confidence, each citing supporting evidence.

1. [file:line-range] — [description of bug]
   Evidence: [claims from Phase 2-3 that support this]
   Confidence: [HIGH/MEDIUM/LOW]

2. [file:line-range] — [description]
   Evidence: [claims]
   Confidence: [HIGH/MEDIUM/LOW]

3. [file:line-range] — [description]
   Evidence: [claims]
   Confidence: [HIGH/MEDIUM/LOW]

FORMAL CONCLUSION:
Root cause is most likely at [file:line-range] because
[summary of evidence chain from phases 2-3].
```

## Template 3: Code Question Answering

Use when answering deep questions about code behavior, semantics, or design.

```
QUESTION: [restate the question precisely]

FUNCTION TRACE TABLE:
| Function/Method | File:Line | Parameters | Return Type | Behavior (VERIFIED) |
|-|-|-|-|-|
| [name] | [file:line] | [types] | [type] | [traced behavior] |
| [name] | [file:line] | [types] | [type] | [traced behavior] |

For each entry, VERIFIED means the implementation was read and traced,
not assumed from the function name.

DATA FLOW ANALYSIS:
Variable: [name]
  Created: [file:line] with value [description]
  Modified: [file:line] by [operation]
  Used: [file:line] in [context]
  Final state: [value/type at point of interest]

[Repeat for each relevant variable]

SEMANTIC PROPERTIES:
For each property claimed:
  Property: [statement about code behavior]
  Evidence: [file:line] — [what the code shows]
  Status: CONFIRMED / REFUTED / UNCERTAIN

ALTERNATIVE HYPOTHESIS CHECK:
What evidence would support the opposite conclusion?
  Hypothesis: [opposite of expected answer]
  Evidence needed: [what would have to be true]
  Search result: [FOUND at file:line / NOT FOUND after checking X, Y, Z]

FORMAL CONCLUSION:
Based on traced execution paths and verified properties,
[answer to the question with key evidence citations].
```

## Template Selection Guide

| Situation | Template | Key Sections |
|-|-|-|
| "Is my diff correct?" | Patch Verification | Test behavior analysis, counterexample search |
| "Are these two patches equivalent?" | Patch Verification | Side-by-side test outcome comparison |
| "Why does test X fail?" | Fault Localization | Code path tracing, divergence analysis |
| "Where is the bug?" | Fault Localization | Hypothesis testing, ranked predictions |
| "How does function X work?" | Code QA | Function trace table, data flow analysis |
| "What happens when Y?" | Code QA | Execution trace, semantic properties |
| "Is behavior Z guaranteed?" | Code QA | Alternative hypothesis check |
