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

**Per-agent model config** is natively supported via `.opencode/agents/` (per-project) or `~/.config/opencode/agents/` (global).

**Planner agent** (`planner.md`):
```yaml
---
description: Planning agent for CRISP-DM steps 1–3 — business context, data understanding, analysis plan
mode: subagent
model: anthropic/claude-opus-4-7
---
[Planner instructions]
```

**Adversarial reviewer agent** (`adversarial-reviewer.md`):
```yaml
---
description: Adversarial reviewer — challenges analysis design and results without re-running analysis
mode: subagent
model: anthropic/claude-opus-4-7
---
[See adversarial reviewer prompt below]
```

**Executor** uses the primary session model — no separate agent file needed unless you want to pin a model.

**Invoking a subagent:**
```
@adversarial-reviewer Review the attached analysis plan for leakage, metric validity, and baseline soundness.
```

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

## Detecting Harness at Runtime

The skill does not auto-detect the harness. At project start, ask:

> "Which harness are you running this in? (Claude Code / OpenCode / Codex / Other)"

Then load the relevant section of this file and proceed. If the user doesn't know or doesn't care, use single-model fallback and skip model configuration.
