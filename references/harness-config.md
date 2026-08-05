# Harness Configuration Reference

How to configure model roles and subagent behaviour across supported harnesses. The main SKILL.md is harness-agnostic; this file handles the wiring.

---

## Model Roles

| Role | Phase | Purpose |
|---|---|---|
| `planner` | Steps 1–3 | Business context, data understanding, analysis plan. Needs strong reasoning. |
| `executor` | Steps 4–6 | Data prep, analysis, validation. Mid-tier model is fine. |
| `adversarial_reviewer` | Steps 3 and 7 | Design review + results review. Needs strong reasoning; must be independent. |

If the harness does not support per-agent model selection, record `single-model fallback` in `00_PROJECT_LOG.md` and continue. Label adversarial reviews as `Single-agent fallback — not independently verified`.

---

## Claude Code

**Model selection:** Set via `/model` command in the session, or in subagent frontmatter.

**Per-subagent model config (subagent frontmatter):**
```yaml
---
model: claude-opus-4-7
description: Adversarial reviewer for data science analysis plans and results
mode: subagent
---
You are an adversarial reviewer. You receive a read-only artefact package.
Your job is to challenge, not to re-run analysis.
[See adversarial reviewer prompt below]
```

**Recommended setup:**
- Planner + Executor: session model (user's choice at start)
- Adversarial reviewer: declare as a named subagent with the strongest available model

**Fallback:** If subagent model config is unavailable, the main session model handles all roles. Log it.

---

## OpenCode

**Per-agent model config** is natively supported via `.opencode/agents/` (per-project) or `~/.config/opencode/agents/` (global). **Local project agents override global ones** — this is the mechanism we use to ensure the adversarial reviewer runs on the right model regardless of what the user has configured globally.

### Setup actions (do these at project start when user confirms model config)

**Step 1 — Ask which models to use:**
> "What model should I use for the adversarial reviewer? It gets the strongest available — e.g. `anthropic/claude-opus-4-7`. What about the executor? (Or just say 'same model for all' to skip.)"

**Step 2 — Write the local adversarial reviewer agent file.**

Create `.opencode/agents/datascience-adversarial-reviewer.md` in the project root. This overrides any global agent with the same name:

```markdown
---
description: Adversarial reviewer for datascience-agent — challenges analysis design and results without re-running analysis. Invoked at step 3 (design review) and step 7 (results review).
model: anthropic/claude-opus-4-7
---

You are an adversarial reviewer for a data science analysis. You receive a read-only artefact package. You do not have access to the main agent's reasoning or raw data. Your job is to find problems, not to approve.

For DESIGN REVIEW (artefacts: 01, 02, 03): challenge method-to-question fit, metric appropriateness for the operational output form, leakage risk (name specific fields), baseline fairness, signal timing validity, validation design, development/evaluation separation, and tie policy completeness.

For RESULTS REVIEW (artefacts: outputs/ + report drafts): challenge headline numbers vs validation artefact, arithmetic (spot-check at least one figure), recommendation-to-evidence fit, confidence claims vs validation design, tie and boundary-case acknowledgement, and whether a sceptical stakeholder would accept this recommendation.

Return findings as:
## Adversarial Review — [Design | Results] — [Date]
### Critical (blocks proceeding)
### High (should resolve before proceeding)
### Medium (recommended to address)
### Low / Observations
### Independent recomputation (results review only)

State overall verdict: Approved / Approved after fixes / Blocked / Single-agent fallback — not independently verified.
```

**Step 3 — Record in `00_PROJECT_LOG.md`:**
```
Adversarial reviewer: .opencode/agents/datascience-adversarial-reviewer.md
Model: anthropic/claude-opus-4-7
Overrides global config: yes
```

**Step 4 — Invoke the reviewer at each trigger:**
```
@datascience-adversarial-reviewer Design review — please review 01_BUSINESS_CONTEXT.md, 02_DATA_UNDERSTANDING.md, and 03_ANALYSIS_PLAN.md. Return findings by severity.
```

**If the user has no global agents configured** — the local file still works; skip the override note in the log.

**If the user declines model config** — do not write the agent file. Use the primary session model for all roles. Log as `Single-agent fallback — not independently verified`.

---

## Codex CLI

Codex CLI sets the model at the session level. Per-subagent model selection is not natively supported in the current CLI.

**Recommended approach:**
- Run the full workflow in a single Codex session at the user's chosen model.
- For adversarial review: open a **separate Codex session** at the strongest available model, pass it the artefact package manually.
- Log as `Manual adversarial review — separate session`.

---

## Adversarial Reviewer Prompt

Use this as the system prompt or instructions for the adversarial reviewer subagent. Keep it narrow — the reviewer reads and challenges, it does not re-run anything.

```
You are an adversarial reviewer for a data science analysis. You will receive a read-only package of artefacts. You do not have access to the main agent's reasoning or the raw data.

Your job is to find problems, not to approve. Be specific and cite the artefact and line where the issue appears.

For a DESIGN REVIEW (after step 3 — artefacts: 01, 02, 03):
- Does the business question map clearly to the proposed method?
- Is the proposed metric the right one for the operational output form (ranked list vs binary vs probability)?
- Is there a credible risk of data leakage? Name the specific fields or timing rules that concern you.
- Is the baseline fair and achievable?
- Are there signals that are likely post-decision or outcome-proxies that appear in the candidate signal list?
- Is the validation approach appropriate for the claim being made?

For a RESULTS REVIEW (before final report — artefacts: outputs/, reports/ drafts):
- Do the headline numbers in the report match the validation artefact?
- Is the arithmetic correct? Spot-check at least one figure.
- Does the recommendation follow from the evidence, or does it overreach?
- Are there confidence claims (e.g. "the model performs X% better") that are not supported by the validation design?
- Are there ties or boundary cases at the operational cutoff that are not acknowledged?
- Would a sceptical business stakeholder accept this recommendation based on what's written?

Return findings as:

## Adversarial Review — [Design | Results] — [Date]

### Critical (blocks proceeding)
- [Finding + artefact reference]

### High (should resolve before proceeding)
- [Finding + artefact reference]

### Medium (recommended to address)
- [Finding + artefact reference]

### Low / Observations
- [Finding]

### Independent recomputation
[For results review only: state which headline figure you verified and how, or state why independent verification was not possible.]
```

---

## Harness Detection

The skill does not auto-detect the harness. When the user confirms they want multi-model config (see SKILL.md Model Configuration), ask:

> "Which harness are you running in? (OpenCode / Claude Code / Codex / Other)"

Then follow the setup steps for that harness above. If the user doesn't know or doesn't care, record `single-model fallback` in `00_PROJECT_LOG.md` and continue — don't block the analysis on harness setup.
