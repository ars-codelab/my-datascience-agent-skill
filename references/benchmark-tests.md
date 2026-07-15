# Skill Benchmark Tests

Use fresh agents or threads. Give them the skill and raw task artifacts, not the expected answer or suspected failure. Remove outputs between runs to prevent contamination.

## Test 1: Missing Context In Collaborative Mode

Prompt:

```text
Use Collaborative mode. I have seller-level Excel data and rules for prioritizing which sellers account managers should contact. Determine whether the rules are good and whether a better simple approach exists using signals available before launch.
```

Expected behavior:

- Search approved memory and state whether relevant context exists.
- Do not inspect Excel or code before business-context approval.
- Ask conversational questions with suggested responses.
- Use native structured-choice UI if the harness supports it; otherwise use compact lettered options.
- Explicitly ask what `pre-launch` means, when the prioritization decision occurs, what action follows, what outcome matters, and what capacity exists.
- Draft a concise `01_BUSINESS_CONTEXT.md` and request explicit sign-off.

Failure examples:

- Infers `pre-launch` from a column name or convenient date.
- Treats missing memory as permission to invent context.
- Produces a long business-context document instead of asking focused questions.
- Uses open-ended prose questions when compact suggested options would work.
- Reads workbook contents before sign-off.

## Test 2: Efficient Excel Handling

After approving business context, ask the agent to continue Data Understanding.

Expected behavior:

- Inspect workbook metadata once.
- Confirm ambiguous sheet and field semantics.
- Build an all-column inventory before narrowing to analysis fields.
- Tag every column or column family as candidate signal, text-to-scan, post-decision/leakage, outcome/proxy, identifier, exclude, or investigate.
- Scan business-named text fields for semantic and structural patterns before dismissing them.
- Profile low-cardinality categorical fields at value level before collapsing them to binary.
- Check parsed date ranges for implausible values or accidental numeric-to-date conversion.
- Load approved data into a dataframe.
- Save and document a fingerprinted interim cache.
- Reuse the cache for subsequent work.
- Keep broad profiles outside `02_DATA_UNDERSTANDING.md`.

Failure examples:

- Repeatedly opens Excel or retrieves cells for every question.
- Selects a small column subset from heuristic keywords before reviewing all columns.
- Ignores text fields such as names, titles, descriptions, or category labels that could contain business signals.
- Performs only keyword text search and misses structural text features such as length, scripts, punctuation, or digit patterns.
- Collapses a categorical field to present/absent before checking value-level differences.
- Treats successful date parsing as sufficient without checking plausible date ranges.
- Creates a cache with no source, sheet, or column invalidation.
- Begins feature construction before Data Understanding approval.

## Test 3: Model Evaluation Integrity

Prompt after prior gates:

```text
Build a transparent scorecard that improves on the existing rules and report top-20-percent performance.
```

Expected behavior:

- Creates and signs off a Model Design Contract.
- Separates scorecard development from final evaluation.
- Uses an out-of-time test or untouched holdout when feasible.
- Distinguishes fixed user benchmarks from agent-developed candidates.
- Confirms whether each method outputs a ranked list, binary flag, probability, category, or threshold.
- Treats words like prioritize, call first, triage, route, queue, or limited capacity as ranking/ordering triggers unless the user confirms otherwise.
- Evaluates binary flags as flagged pools under limited capacity unless an approved ranking or tie-breaker exists.
- Uses operational cutoffs such as call capacity, review volume, or queue size when relevant.
- Documents and tests tie handling at the top-20-percent boundary.
- Checks score spread and tie density before accepting a scorecard as operationally usable.
- Removes or justifies weak, contradictory, or overlapping signals that duplicate stronger correlated signals.
- Labels results exploratory if independent evaluation is impossible.

Failure examples:

- Selects signals, weights, or cutoffs and evaluates them on the same full cohort.
- Uses hidden secondary sorting.
- Treats a binary flag as a ranked list through arbitrary top-K sorting.
- Reports exact top-k metrics without discussing boundary ties.
- Accepts a compressed scorecard with heavy cutoff ties as if it were a useful ranking.

## Test 4: Independent Final Review

Prompt after modeling outputs exist:

```text
Let's finalize the result and write the business report.
```

Expected behavior:

- Runs adversarial final review before writing polished final reports.
- Uses an independent reviewer when supported.
- Inspects code, not only documents.
- Independently recomputes at least one headline number.
- Checks evaluation separation, timing, leakage, denominators, and ties.
- Checks that operational output and metrics match the business action before report writing.
- Checks that column inventory, text-field scan, and operational output framing were sufficient.
- Writes or revises final reports only after resolving Critical/High findings.
- Adds a post-report consistency check to `07_FINAL_REVIEW.md`.
- Final business report passes arithmetic, calibrated wording, required-field, duplicate-signal, decision-time, and population-clarity checks.
- Final business report uses one source-of-truth table for repeated headline numbers, required fields, populations, and caveats.
- Blocks or qualifies publication when executable verification is unavailable.

Failure examples:

- Writes the business report first, then runs review only if the user asks.
- Treats final review as a Markdown completeness pass.
- Leaves Critical or High findings unresolved in the published recommendation.
- Publishes inconsistent weekly/monthly/team math, overclaims predictions as certainty, omits required fields, double-counts related signals, or mixes post-decision findings into a pre-decision recommendation.
- Recomputes or paraphrases the same number in multiple places without reconciling to a single reviewed source.

## Scoring

| Score | Behavior |
|---:|---|
| 5 | Respects gates, asks efficiently when context is missing, uses cached dataframe ingestion, applies honest evaluation, and completes adversarial review. |
| 4 | Correct process with minor verbosity or documentation issues. |
| 3 | Respects gates but makes light assumptions, handles data inefficiently, or performs weak validation. |
| 2 | Violates a material rule but detects and repairs it without user prompting. |
| 1 | Repairs only after the user identifies the violation. |
| 0 | Continues with assumptions or invalid evidence after the defect is known. |

Record scores separately for gate discipline, assumption control, interaction quality, ingestion efficiency, signal discovery, operational framing, scientific validity, and final review. A high average cannot compensate for a zero in gate discipline or scientific validity.
