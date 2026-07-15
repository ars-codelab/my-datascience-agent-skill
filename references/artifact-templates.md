# CRISP-DM Artifact Templates

Use these templates as concise checklists. Include only decision-relevant content and follow the length limits in `SKILL.md`.

## Contents

- Project log
- Business context
- Data understanding
- Analysis plan
- Data preparation
- Modeling or analysis notes
- Validation and risks
- Final review
- Retrospective

## `00_PROJECT_LOG.md`

```markdown
# Project Log

- Analysis slug:
- Mode:
- Start date:
- Current phase:
- Decision supported:
- Context coverage: High / Partial / Low or none
- Memory and documentation searched:

## Timeline

| Date/time | Event or decision | Artifact | Implication |
|---|---|---|---|

## Changes, Waivers, And Violations

| Item | Previous state | New state | Reason | Approved by |
|---|---|---|---|---|

## Open Items

| Item | Owner | Blocking? | Needed by |
|---|---|---|---|
```

## `01_BUSINESS_CONTEXT.md`

Target: 300-500 words.

```markdown
# Business Context

## Decision

State the decision in 1-2 sentences.

## Intended Use

| Question | Approved answer |
|---|---|
| Who uses the output? |  |
| What action will they take? |  |
| When will they use it? |  |
| What changes for a positive, negative, or uncertain result? |  |

## Problem And Current Baseline

Summarize the problem and how the decision is made today.

## Key Context

- KPI or outcome:
- Capacity or operating constraint:
- Current workflow or SOP:
- Incentive or behavior risk:
- Decision cadence:

## Scope

- In:
- Out:

## Material Assumptions

| Assumption | Confirmed / Inferred / Needs confirmation | Source |
|---|---|---|

## Open Questions

Include at most 3-5 questions that block Data Understanding.

## Sign-Off

[Standard sign-off block]
```

## `02_DATA_UNDERSTANDING.md`

Target: 500-900 words plus essential tables. Keep broad profiling in `outputs/diagnostics/`.

```markdown
# Data Understanding

## Sources

| Source | Format | Grain | Rows / sheets | Role | Provenance |
|---|---|---|---:|---|---|

## Unit, Keys, And Relevant Fields

| Concept or field | Meaning | Type | Timing | Missingness | Status |
|---|---|---|---|---:|---|

## Decision-Time And Date Semantics

| Concept | Approved definition | Field(s) | Status |
|---|---|---|---|
| Decision timestamp |  |  |  |
| Event or launch timestamp |  |  |  |
| Fallback date logic |  |  |  |
| Outcome window |  |  |  |
| Maturity / censoring rule |  |  |  |
| Late-arriving / backfilled data policy |  |  |  |

## Outcome Candidates

| Outcome | Definition | Window | Coverage | Risk | Status |
|---|---|---|---:|---|---|

## Candidate Signal Inventory

| Signal family | Field(s) | Business meaning | Available at decision time? | Leakage risk | Status |
|---|---|---|---|---|---|

Known heuristics, prior models, must-include signals, and forbidden signals:

## Data Quality And Fitness

| Finding | Evidence path | Decision impact | Proposed handling |
|---|---|---|---|

Minimum checks: row and entity counts, duplicate keys, missingness, ranges, invalid values, join coverage, label coverage, and time coverage.

## Dataframe And Cache Record

| Item | Value |
|---|---|
| Source file and sheet |  |
| Engine |  |
| Columns loaded |  |
| Rows loaded |  |
| Cache path and format |  |
| Source fingerprint |  |
| Invalidation conditions |  |

## Open Risks

## Sign-Off

[Standard sign-off block]
```

Do not approve while material field or date semantics remain unresolved.

## `03_ANALYSIS_PLAN.md`

Target: 600-900 words.

```markdown
# Analysis Plan

## Testable Question

## Recommended Method And Why

## Model Design Contract

| Decision | Approved plan |
|---|---|
| Fixed benchmarks / current process |  |
| Candidate signal universe |  |
| Must-include / forbidden signals |  |
| Development / validation / final test |  |
| Operational capacity and cutoffs |  |
| Tie policy |  |
| Interpretability / implementation constraints |  |

## Population, Target, And Timing

| Item | Definition | Risk |
|---|---|---|

## Baselines And Alternatives

| Candidate | Why included or rejected |
|---|---|

## Metrics

| Metric | Decision meaning | Failure mode |
|---|---|---|

## Validation And Robustness

Specify time split, holdout, cross-validation, backtest, sensitivity checks, and uncertainty reporting. Separate model development from final evaluation.

## Leakage And Causality Guardrails

## Planned Outputs

## Stop Or Escalate Criteria

## Sign-Off

[Standard sign-off block]
```

## `04_DATA_PREPARATION.md`

```markdown
# Data Preparation

## Inputs And Cached Data

| Source | Cache | Raw rows | Grain |
|---|---|---:|---|

## Transformations

| Step | Code | Input | Output | Row impact | Reason |
|---|---|---|---|---:|---|

## Joins And Deduplication

| Operation | Keys / rule | Coverage or row impact | Rationale |
|---|---|---:|---|

## Features And Outcomes

| Field | Logic | Source fields | Timing-safe? | Notes |
|---|---|---|---|---|

## Final Dataset

- Path:
- Grain:
- Rows and entities:
- Date range:
- Outcome coverage:

## Open Issues And Sign-Off
```

## `05_MODELING_OR_ANALYSIS_NOTES.md`

```markdown
# Modeling Or Analysis Notes

## Dataset And Evaluation Role

State whether each dataset is development, validation, or untouched final test data.

## Methods Run

| Method | Purpose | Code | Parameters | Status |
|---|---|---|---|---|

## Baseline And Main Results

| Candidate | Dataset | Metric | Result | Uncertainty | Interpretation |
|---|---|---|---:|---|---|

## Error, Segment, Sensitivity, And Tie Analysis

## Evidence Versus Interpretation

Separate observations, inferences, and recommendations.

## Sign-Off
```

## `06_VALIDATION_AND_RISKS.md`

```markdown
# Validation And Risks

## Readiness Summary

## Claim Strength

| Claim | Strong / Moderate / Weak / Unsupported | Reason |
|---|---|---|

## Validation Integrity

| Check | Pass / fail | Evidence |
|---|---|---|
| Final test was untouched during development |  |  |
| Code matches documented model / rule |  |  |
| Cutoffs and ties are handled transparently |  |  |
| Cohorts, denominators, periods, and baselines match |  |  |

## Leakage, Causality, Robustness, And Bias

| Risk or check | Result | Impact | Mitigation |
|---|---|---|---|

## Limitations And Operational Risks

## Recommendation Readiness

## Sign-Off
```

## `07_FINAL_REVIEW.md`

Use the detailed review requirements in `references/review-and-retrospective.md`.

```markdown
# Final Review

- Reviewer and independence status:
- Artifacts reviewed:
- Execution / recomputation performed:

## Findings

| Severity | Finding | Evidence | Required fix |
|---|---|---|---|

## Claim-To-Evidence Check

| Claim | Evidence path | Recomputed? | Result |
|---|---|---|---|

## Publication Decision

- Approved / approved after fixes / blocked / not independently verified:
- Required fixes:
- Reviewer:
- Date:
```

## `08_RETROSPECTIVE.md`

```markdown
# Retrospective

## Outcome And Reference Comparison

- Decision or use:
- Expected or reference result:
- Agent result:
- Material gap:

## What Worked

## Failure Analysis

| Failure | Consequence | Root cause | Artifact or control that should have caught it | Prevention |
|---|---|---|---|---|

## Information Gaps And Resolution

| Missing information | Search / infer / ask / stop next time | Why |
|---|---|---|

## User Involvement

| User input | Necessary? | How to make it lower effort next time |
|---|---|---|

## Reusable Context And Skill Improvements

Save only approved, anonymized, non-sensitive context.

## Status

Do not mark Approved or name the user as reviewer without explicit approval.
```
