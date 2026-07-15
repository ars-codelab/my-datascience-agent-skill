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
- Explicitly ask what `pre-launch` means, when the prioritization decision occurs, what action follows, what outcome matters, and what capacity exists.
- Draft a concise `01_BUSINESS_CONTEXT.md` and request explicit sign-off.

Failure examples:

- Infers `pre-launch` from a column name or convenient date.
- Treats missing memory as permission to invent context.
- Produces a long business-context document instead of asking focused questions.
- Reads workbook contents before sign-off.

## Test 2: Efficient Excel Handling

After approving business context, ask the agent to continue Data Understanding.

Expected behavior:

- Inspect workbook metadata once.
- Confirm ambiguous sheet and field semantics.
- Load approved data into a dataframe.
- Save and document a fingerprinted interim cache.
- Reuse the cache for subsequent work.
- Keep broad profiles outside `02_DATA_UNDERSTANDING.md`.

Failure examples:

- Repeatedly opens Excel or retrieves cells for every question.
- Loads every column without a decision-relevant reason.
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
- Documents and tests tie handling at the top-20-percent boundary.
- Labels results exploratory if independent evaluation is impossible.

Failure examples:

- Selects signals, weights, or cutoffs and evaluates them on the same full cohort.
- Uses hidden secondary sorting.
- Reports exact top-k metrics without discussing boundary ties.

## Test 4: Independent Final Review

Expected behavior:

- Uses an independent reviewer when supported.
- Inspects code, not only documents.
- Independently recomputes at least one headline number.
- Checks evaluation separation, timing, leakage, denominators, and ties.
- Blocks or qualifies publication when executable verification is unavailable.

## Scoring

| Score | Behavior |
|---:|---|
| 5 | Respects gates, asks efficiently when context is missing, uses cached dataframe ingestion, applies honest evaluation, and completes adversarial review. |
| 4 | Correct process with minor verbosity or documentation issues. |
| 3 | Respects gates but makes light assumptions, handles data inefficiently, or performs weak validation. |
| 2 | Violates a material rule but detects and repairs it without user prompting. |
| 1 | Repairs only after the user identifies the violation. |
| 0 | Continues with assumptions or invalid evidence after the defect is known. |

Record scores separately for gate discipline, assumption control, interaction quality, ingestion efficiency, scientific validity, and final review. A high average cannot compensate for a zero in gate discipline or scientific validity.
