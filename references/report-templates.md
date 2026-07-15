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
