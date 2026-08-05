# Systematic Data Science Agent

A CRISP-DM-structured AI skill that slows down before it executes. Designed for analysts and product practitioners who want AI to be a rigorous thinking partner — not a fast-moving black box.

---

## The Problem This Solves

When you ask an AI to do complex analysis, it immediately starts executing. It assumes what "active" means, picks a metric, chooses a date field, and writes code — all before you've agreed on what question you're actually answering.

This produces fast outputs and unreliable conclusions.

This skill inverts that pattern. The AI clarifies before it assumes, documents before it executes, and waits for your sign-off before advancing to the next phase. Every material assumption is surfaced, agreed, and recorded. The result is analysis you can defend, reproduce, and hand off.

---

## How It Works

The skill follows **CRISP-DM** — a battle-tested data science process framework — structured as a series of explicit gates. The AI cannot advance to the next phase until you've approved the current one.

```
Business Context → Data Understanding → Analysis Plan
       ↓                   ↓                  ↓
  [Sign-off]          [Sign-off]    [Sign-off + Design Review]
                                              ↓
                              Data Prep → Analysis → Validation
                                              ↓
                                    [Results Review]
                                              ↓
                                      Final Report
                                              ↓
                                       Retrospective ← triggered automatically
```

**Adversarial review happens twice** — after the analysis plan (cheap, catches design flaws before any code runs) and before the final report (catches unsupported claims). The reviewer reads artefacts only; it never re-runs analysis.

**The retrospective is proactive.** After every project, the AI prompts a 5-minute review before you close. Learnings feed back into the skill itself.

---

## Modes

| Mode | Who it's for |
|---|---|
| **Guided** | Non-specialists or first-time users. AI fills in reasonable defaults, explains every choice in plain language, and asks 1–2 questions at a time. |
| **Collaborative** | Practitioners who want to stay in control. AI presents options with tradeoffs and a recommended path. Default mode. |
| **Expert** | Experienced data scientists. Concise, technical, reproducibility and review gates active by default. |

---

## Multi-Model Architecture (Optional)

If your harness supports per-agent model selection, you can assign different models to different roles — spending more on thinking, less on execution.

| Role | Phase | Suggested tier |
|---|---|---|
| **Planner** | Business context, data understanding, analysis plan | Strongest available |
| **Executor** | Data prep, analysis, validation | Mid-tier |
| **Adversarial Reviewer** | Design review + results review | Strongest available |

Supported out of the box on Claude Code and OpenCode. Codex CLI falls back gracefully to a separate session for adversarial review. See `references/harness-config.md` for setup.

---

## Repository Structure

```
datascience-agent/
  SKILL.md                          ← main skill instructions (load this)
  README.md                         ← you are here
  references/
    artifact-templates.md           ← CRISP-DM artefact templates and sign-off blocks
    report-templates.md             ← business and technical report templates
    data-ingestion.md               ← reading Excel, CSV, databases, MCP sources
    harness-config.md               ← per-harness model config + adversarial reviewer prompt
    self-improvement.md             ← retrospective protocol and contribution guide
    benchmark-tests.md              ← test cases for evaluating skill quality
```

Each reference file is loaded on demand — only when the current phase needs it. The main `SKILL.md` stays in context throughout.

---

## Getting Started

### Using the skill in Claude Code

```bash
# Clone the repo
git clone https://github.com/your-username/datascience-agent

# Add SKILL.md to your project's .claude/skills/ directory, or reference it directly
cp datascience-agent/SKILL.md .claude/skills/datascience-agent.md
```

Then start a session and say: *"Run the data science skill. Here's my question: [your business question]."*

### Using in OpenCode

```bash
cp datascience-agent/SKILL.md ~/.config/opencode/skills/datascience-agent.md
```

### Using in Codex CLI

Reference the skill file at session start:

```bash
codex --system-prompt "$(cat datascience-agent/SKILL.md)" "Here's my analysis question: ..."
```

---

## What Gets Created Per Project

Each project lives in its own folder. Folders are created lazily — only when a file is actually written there.

```
analysis_slug/
  00_PROJECT_LOG.md          ← mode, model config, gate waivers, violations
  01_BUSINESS_CONTEXT.md     ← decision, KPI, scope, constraints [sign-off required]
  02_DATA_UNDERSTANDING.md   ← column inventory, semantics, fitness [sign-off required]
  03_ANALYSIS_PLAN.md        ← method, metrics, validation design [sign-off + design review]
  04_DATA_PREPARATION.md     ← transformations, row impact, feature logic
  05_ANALYSIS_NOTES.md       ← methods, parameters, results, errors
  06_VALIDATION.md           ← leakage audit, robustness, recommendation readiness
  07_FINAL_REVIEW.md         ← adversarial results review findings and resolutions
  08_RETROSPECTIVE.md        ← what worked, what didn't, skill improvement notes
  data/
  code/
  outputs/
  reports/
  memory/
    reusable_context.md      ← approved definitions and patterns for this project
    skill_improvement_notes.md  ← generalizable improvements across projects
```

---

## The Self-Improvement Loop

This skill is designed to get better with use.

After every project, the AI proactively prompts a short retrospective. Findings go to two places:

- **`memory/reusable_context.md`** — project-local. Approved KPI definitions, field semantics, data quality patterns. Reused in future analyses in this repo.
- **`memory/skill_improvement_notes.md`** — accumulates across projects. Process gaps, better question phrasing, missing guardrails. This is your backlog for improving the skill.

### Fork → improve → contribute

```
Fork this repo
    ↓
Run projects, collect retrospective notes in skill_improvement_notes.md
    ↓
Edit your local SKILL.md or references/ based on what you've learned
    ↓
Test on the next project
    ↓
If the improvement is generalizable → open a PR
```

**What stays in your fork:** domain-specific KPI definitions, org-specific data quality patterns, question options tuned to your team's vocabulary, harness config for your setup.

**What's worth a PR:** fixes to questions that were ambiguous across domains, missing scientific guardrails, gate calibration improvements, Guided mode improvements for less experienced users, new harness support.

See `references/self-improvement.md` for the full contribution guide and PR tagging conventions.

---

## Design Principles

**Clarify before assuming.** Undefined terms — `active`, `churned`, `conversion`, `high value` — are never silently translated into code. They're surfaced as questions.

**Gate before executing.** No data exploration until business context is signed off. No feature engineering until data understanding is signed off. No report writing until adversarial review is clean.

**Artefacts are decision surfaces, not data dumps.** The business context document is 300–500 words. The analysis plan is 600–900 words. Detailed evidence lives in `outputs/` — the gated artefacts stay readable.

**Adversarial review is scoped, not redundant.** The reviewer gets a read-only artefact package. It challenges and returns structured findings. It does not re-run analysis. The expensive model is used for thinking, not for repeating work.

**The retrospective closes the loop.** Learnings don't disappear into chat history. They're written to files that persist, accumulate, and feed back into the skill.

---

## Contributing

PRs welcome. One change per PR. Include the retrospective note that motivated the change and describe what was broken without it.

Tag your PR: `mode:guided` · `mode:expert` · `gate` · `question-quality` · `harness` · `scientific-guardrail` · `self-improvement`

Open an issue first for anything that touches the non-negotiable rules or gate structure — those changes benefit from discussion before implementation.

---

## License

MIT
