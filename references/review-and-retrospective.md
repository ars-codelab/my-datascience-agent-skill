# Independent Review And Retrospective

## Independent Review

Use a fresh agent or model call when the harness supports it. Prefer the strongest suitable available model for final business findings review, especially when the working agent used a cheaper or faster model. Provide raw artifacts and the review task. Do not provide the main agent's private reasoning, intended answer, or a conclusion-biased summary.

The reviewer must behave as a falsifier, not a document-completeness checker.

Run this review before polished final reports when the user says to finalize, publish, wrap up, hand off, or write the business report. If the project provides an `adversarial_test` script, prompt, checklist, or tool, run it during this step. Final reports should incorporate the review findings, not be the first place the recommendation is made.

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
10. For capacity-constrained actions, verify that the primary metric matches the operating volume or queueing decision.
11. Inspect ranking ties, score range, distinct score count, largest tie groups at operating cutoffs, and any secondary sorting logic.
12. Confirm the data understanding artifact includes a full-column inventory and that important business-named text fields were scanned or explicitly ruled out.
13. Attempt at least one plausible alternative explanation, sensitivity, or counterexample.
14. Verify that every material claim maps to evidence and that reports expose unresolved risks.

After final reports are drafted, perform a short consistency check: business report, technical report, and evidence map must not introduce claims, numbers, or recommendations that were not reviewed or evidence-backed.

Reviewing only Markdown consistency is insufficient.

### Review Repair Loop

Return review findings to the working agent. The working agent must resolve Critical and High findings before publication.

If review changes the analysis state, do not patch only the final report. Reopen the affected upstream artifacts, mark stale versions `Superseded`, and update the artifact chain before final reporting:

- `05_MODELING_OR_ANALYSIS_NOTES.md` for changed models, features, scores, metrics, or tie behavior.
- `06_VALIDATION_AND_RISKS.md` for changed claim strength, leakage, robustness, limitations, or recommendation readiness.
- `reports/evidence_map.md` or the source-of-truth table for changed headline values, field lists, populations, caveats, or recommendation wording.
- `07_FINAL_REVIEW.md` with what was found, what changed, and which artifacts were updated.

Then rerun the relevant review checks. A report may not cite a result that appears only in the agent's memory or final prose.

### Review Status

- `Approved`: independent executable review found no unresolved Critical or High issue.
- `Approved after fixes`: required fixes were completed and rechecked.
- `Blocked`: unresolved issue could change the conclusion, create material risk, or prevent reproduction.
- `Single-agent fallback - not independently verified`: no independent reviewer or executable recomputation was available.

For Expert Strict or high-stakes work, the fallback status blocks publication unless an accountable expert explicitly accepts it.

### Suggested Review Prompt

```text
Act as an independent data science reviewer. Review this analysis from first principles and try to falsify its recommendation before final reports are written. Read the signed-off business and data definitions, inspect the code and outputs, verify development/test separation, independently recompute at least one headline metric, check timing and leakage, confirm methods are evaluated according to their actual operational output and capacity constraints, inspect cutoff ties and hidden sorting, check that the column inventory and text-field scan were sufficient, and trace every major claim through evidence. Specifically check whether final claims are supported by current upstream artifacts rather than stale notes or agent memory. Report findings by severity and return Approved, Approved after fixes, Blocked, or Single-agent fallback - not independently verified.
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
