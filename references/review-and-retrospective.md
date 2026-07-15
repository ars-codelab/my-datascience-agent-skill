# Independent Review And Retrospective

## Independent Review

Use a fresh agent or model call when the harness supports it. Provide raw artifacts and the review task. Do not provide the main agent's private reasoning, intended answer, or a conclusion-biased summary.

The reviewer must behave as a falsifier, not a document-completeness checker.

### Required Review Work

1. Read the signed-off business context, data understanding, and analysis plan first.
2. Verify that the final use, target, population, timing, and metrics still match those approvals.
3. Inspect executable code and compare it with documented rules, features, transformations, cutoffs, and tie policies.
4. Verify separation between model development and final evaluation.
5. Independently recompute at least one headline metric from analysis-ready data or lower-level outputs.
6. Check decision-time availability, delayed fields, fallback dates, outcome maturity, and leakage.
7. Check denominators, cohorts, periods, baselines, missing-value treatment, joins, and deduplication.
8. Verify that each method is evaluated according to its actual operational output: ranked list, binary flag, probability, category, or narrative recommendation.
9. For binary flags used under limited capacity, compare random-from-flagged-pool or an approved tie-breaker; do not accept hidden top-K sorting.
10. Inspect ranking ties and any secondary sorting logic.
11. Confirm the data understanding artifact includes a full-column inventory and that important business-named text fields were scanned or explicitly ruled out.
12. Attempt at least one plausible alternative explanation, sensitivity, or counterexample.
13. Verify that every material claim maps to evidence and that reports expose unresolved risks.

Reviewing only Markdown consistency is insufficient.

### Review Status

- `Approved`: independent executable review found no unresolved Critical or High issue.
- `Approved after fixes`: required fixes were completed and rechecked.
- `Blocked`: unresolved issue could change the conclusion, create material risk, or prevent reproduction.
- `Single-agent fallback - not independently verified`: no independent reviewer or executable recomputation was available.

For Expert Strict or high-stakes work, the fallback status blocks publication unless an accountable expert explicitly accepts it.

### Suggested Review Prompt

```text
Act as an independent data science reviewer. Review this analysis from first principles and try to falsify its recommendation. Read the signed-off business and data definitions, inspect the code and outputs, verify development/test separation, independently recompute at least one headline metric, check timing and leakage, confirm methods are evaluated according to their actual operational output, inspect cutoff ties and hidden sorting, check that the column inventory and text-field scan were sufficient, and trace every major claim through the evidence map. Report findings by severity and return Approved, Approved after fixes, Blocked, or Single-agent fallback - not independently verified.
```

## Retrospective

Run the retrospective after the result is delivered or compared with a reference evaluation.

### Required Questions

- What was expected or provided as a reference answer?
- What did the agent produce, and what material gap remained?
- Which errors were analytical, semantic, implementation, communication, or process failures?
- Which artifact or review control should have caught each failure?
- Did the process ask the user too little, too much, or at the wrong time?
- For each missing fact, should the next agent search, infer, ask, or stop?
- What reusable non-sensitive context should be saved after approval?
- What skill rule, template, test, or tool should change?

Do not frame missing user instructions as the root cause when the agent could reasonably have searched, discovered, tested, or asked. Human attention is expensive, but silent assumption is not an acceptable substitute.

Do not mark the retrospective approved or name the user as reviewer until the user explicitly approves it.

## Memory Update

Save only approved, anonymized, reusable context. Do not save raw data, proprietary schema details, personal data, confidential team information, or unsupported conclusions.

Classify each proposed memory item as:

- Project-local context.
- Reusable analytical pattern.
- Metric or business definition.
- Known data-quality issue.
- Process or skill improvement.

Future analyses may use approved memory to draft context, but must reconfirm material facts that are time-sensitive or specific to a different population or decision.
