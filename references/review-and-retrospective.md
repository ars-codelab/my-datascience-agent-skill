# Independent Review

> This file covers the **Data Scientist mode** adversarial review protocol only. Retrospective and skill improvement are handled in `references/self-improvement.md`.

---

## When Reviews Run

Two triggers — scoped to minimise token cost while maximising ROI:

| Trigger | Artefacts the reviewer receives | Purpose |
|---|---|---|
| **Design review** — after step 3 sign-off | `01_BUSINESS_CONTEXT.md`, `02_DATA_UNDERSTANDING.md`, `03_ANALYSIS_PLAN.md` | Catches flawed design before any code runs. Cheapest review; highest ROI. |
| **Results review** — before final report | `outputs/` + report drafts only | Catches unsupported claims, arithmetic errors, recommendation overreach. |

The reviewer never receives the main agent's reasoning chain. It reads artefacts and challenges them.

---

## Reviewer Behaviour

The reviewer is a falsifier, not a document-completeness checker. Provide raw artefacts and the review task only. Do not provide the main agent's intended answer or a conclusion-biased summary.

Use the reviewer model and harness recorded in `00_PROJECT_LOG.md`. If the requested model is unavailable, apply the configured fallback rule and update the log before reviewing.

---

## Design Review Checklist (After Step 3)

1. Does the business question map clearly to the proposed method?
2. Is the proposed metric the right one for the operational output form (ranked list vs binary flag vs probability vs narrative)?
3. Is there a credible risk of data leakage? Name the specific fields or timing rules that concern you.
4. Is the baseline fair, achievable, and matched to the capacity constraint?
5. Are there signals in the candidate list that are likely post-decision or outcome-proxies?
6. Is the validation approach appropriate for the claim being made?
7. Is the development/evaluation separation plan sound?
8. Are tie policy and score-spread acceptance criteria defined?

---

## Results Review Checklist (Before Final Report)

1. Read the signed-off business context, data understanding, and analysis plan first. Verify that final use, target, population, timing, and metrics still match those approvals.
2. Inspect executable code and compare it with documented rules, features, transformations, cutoffs, and tie policies.
3. Verify separation between model development and final evaluation.
4. Independently recompute at least one headline metric from analysis-ready data or lower-level outputs.
5. Check decision-time availability, delayed fields, fallback dates, outcome maturity, and leakage.
6. Check denominators, cohorts, periods, baselines, missing-value treatment, joins, and deduplication.
7. Verify that each method is evaluated according to its actual operational output: ranked list, binary flag, probability, category, or narrative.
8. For binary flags used under limited capacity, verify the random-from-flagged-pool or approved tie-breaker is applied; do not accept hidden top-K sorting.
9. For capacity-constrained actions, verify that the primary metric matches the operating volume or queueing decision.
10. Inspect ranking ties, score range, distinct score count, largest tie groups at operating cutoffs, and any secondary sorting logic.
11. Confirm the data understanding artefact includes a full-column inventory and that business-named text fields were scanned or explicitly ruled out.
12. Attempt at least one plausible alternative explanation, sensitivity, or counterexample.
13. Verify that every material claim maps to evidence and that reports expose unresolved risks.

After final reports are drafted, run a short consistency check: business report, technical report, and evidence map must not introduce claims, numbers, or recommendations that were not reviewed or evidence-backed.

Reviewing only Markdown consistency is insufficient.

---

## Review Repair Loop

Return findings to the working agent. The working agent must resolve Critical and High findings before publication.

If review changes the analysis state, do not patch only the final report. Reopen the affected upstream artefacts, mark stale versions `Superseded`, and update the artefact chain:

- `05_ANALYSIS_NOTES.md` — changed methods, features, scores, metrics, or tie behaviour
- `06_VALIDATION_AND_RISKS.md` — changed claim strength, leakage, robustness, limitations, or recommendation readiness
- `reports/evidence_map.md` — changed headline values, field lists, populations, caveats, or recommendation wording
- `07_FINAL_REVIEW.md` — what was found, what changed, which artefacts were updated

Then rerun the relevant review checks. A report may not cite a result that appears only in the agent's memory or final prose.

---

## Review Status

| Status | Meaning |
|---|---|
| `Approved` | Independent review found no unresolved Critical or High issue |
| `Approved after fixes` | Required fixes completed and rechecked |
| `Blocked` | Unresolved issue could change the conclusion, create material risk, or prevent reproduction |
| `Single-agent fallback — not independently verified` | No independent reviewer or executable recomputation was available |

In Expert mode or high-stakes work, the fallback status blocks publication unless an accountable expert explicitly accepts it.

---

## Adversarial Reviewer Prompt

Use this as the system prompt or instructions for the adversarial reviewer agent. The full harness-specific setup is in `references/harness-config.md`.

**Design review prompt (step 3):**
```
Act as an adversarial reviewer for a data science analysis plan. You receive artefacts 01, 02, and 03 only. You do not have access to the main agent's reasoning or the raw data.

Your job is to find problems, not to approve. Be specific and cite the artefact and section where the issue appears.

Check: method-to-question fit, metric appropriateness for the operational output form, leakage risk (name specific fields or timing rules), baseline fairness, signal timing validity, validation design soundness, development/evaluation separation, and tie policy completeness.

Return findings as Critical / High / Medium / Low with artefact references. State your overall verdict: Approved / Approved with concerns / Blocked.
```

**Results review prompt (step 7):**
```
Act as an adversarial reviewer for a completed data science analysis. You receive outputs and report drafts only — not the main agent's reasoning chain. Your job is to falsify the recommendation before the final report is written.

Check: headline numbers against validation artefact, arithmetic correctness (spot-check at least one figure), recommendation-to-evidence fit, confidence claims against validation design, tie and boundary-case acknowledgement at operating cutoffs, and whether a sceptical business stakeholder would accept this recommendation as written.

Independently recompute at least one headline metric. State whether independent verification was possible and what you verified.

Return findings as Critical / High / Medium / Low. State your overall verdict: Approved / Approved after fixes / Blocked / Single-agent fallback — not independently verified.
```
