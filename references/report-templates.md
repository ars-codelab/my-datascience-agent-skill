# Final Report Templates

Write final reports only after `07_FINAL_REVIEW.md` contains a pre-report adversarial review or a clearly labeled fallback review. Resolve Critical and High findings before presenting the recommendation as decision-ready.

## Writing Standard

- Start with the answer, decision, evidence, and most important caveat.
- Use concise paragraphs and concrete numbers.
- Pair every metric with its population, period, and baseline.
- Separate fact, interpretation, and recommendation.
- Define necessary technical terms in plain language.
- State uncertainty and decision consequences, not generic caveats.
- Never present association as causation.
- Exclude sensitive raw data.
- Make recommendations actionable and proportional to evidence strength.

Use `We recommend` only for decision-ready evidence. Use `The evidence suggests` when uncertainty is material and `We cannot conclude` when the requested claim is unsupported.

## Report Synthesis Protocol

Treat final reports as a fresh synthesis from reviewed evidence, not a copy-forward from analysis notes.

Before drafting, create or update a compact source-of-truth table for:

| Item | Reviewed value | Evidence path | Report wording |
|---|---|---|---|
| Recommendation |  |  |  |
| Primary metric and baseline |  |  |  |
| Weekly / monthly / team extrapolation |  |  |  |
| Required production fields |  |  |  |
| Deployment population |  |  |  |
| Validation population |  |  |  |
| Main caveat |  |  |  |

Use the table as the only source for repeated headline numbers and caveats. If a number is derived, write the formula once and reuse the derived value everywhere.

## `reports/business_report.md`

```markdown
# [Title] - Business Report

## Executive Summary

In 3-5 short paragraphs, state the recommendation, main evidence, decision impact, and primary caveat.

## Decision And Intended Use

## Recommendation

| Action | Rationale | Expected benefit | Risk / condition |
|---|---|---|---|

## Key Findings

| Finding | Evidence | Business implication |
|---|---|---|

## Comparison With Current Process

| Option | Population / capacity | Result | Tradeoff |
|---|---:|---:|---|

## How To Use The Result

State thresholds, priority tiers, escalation paths, or next decisions.

List every required production input field and timing condition. If two scorecard rows use related fields, explain why both are needed or combine them.

## Caveats, Monitoring, And Follow-Up

| Risk or unknown | Effect on decision | Monitoring / next step | Owner |
|---|---|---|---|
```

## `reports/technical_report.md`

```markdown
# [Title] - Technical Report

## Status

State the conclusion and conditions it depends on.

## Question, Intended Use, Population, And Timing

## Data Sources And Preparation

| Source | Grain | Rows | Role | Provenance |
|---|---|---:|---|---|

## Target And Signal Policy

Document decision timestamp, outcome window, allowed signals, exclusions, and leakage controls.

## Method And Evaluation Design

Describe baselines, development/test separation, methods, metrics, uncertainty, capacity cutoffs, and tie policy.

## Results

| Candidate | Evaluation dataset | Metric | Result | Baseline | Uncertainty |
|---|---|---|---:|---:|---|

## Robustness, Error, Segment, Leakage, Bias, And Causality Review

## Limitations And Operational Notes

## Reproducibility

List data fingerprints, cache, code, environment, commands, and output paths.

## Conclusion
```

## `reports/evidence_map.md`

```markdown
# Evidence Map

| Claim | Report section | Data | Code | Output | Recomputed in review? | Strength |
|---|---|---|---|---|---|---|

## Restricted Artifacts

| Artifact | Restriction reason | Safe aggregate replacement |
|---|---|---|

## Reproduction Notes

## Unresolved Review Notes
```

## Final Writing Check

Confirm the first page answers what to do, why, for whom, under which assumptions, and with what risk. Confirm every headline number traces through the evidence map and uses the correct denominator and baseline.

Before publication, check:

- Arithmetic consistency: recompute every headline lift, weekly/monthly conversion, team-size extrapolation, percentage, and rounded number.
- Single-source consistency: each repeated number or caveat must come from the source-of-truth table, not be recalculated independently in prose.
- Wording calibration: use predicted, scored, estimated, or expected when the model cannot know the true future state.
- Operational completeness: list all fields required to apply the recommendation, including date fields needed for timing-safe logic.
- Signal clarity: avoid duplicate or overlapping scorecard signals unless the distinction is explicit and justified.
- Decision-time clarity: do not present post-decision or post-launch findings as deployable pre-decision rules; move them to caveats or follow-up ideas.
- Population clarity: state whether validation used the exact deployment population or historical data with decision-time-safe fields.
- Evidence consistency: ensure business report, technical report, evidence map, and final review use the same recommendation, numbers, baselines, and caveats.
