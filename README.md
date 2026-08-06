# A Trustable Data Science Agent

From a quick exploratory analysis to a rigorous causal study. The agent reads your request, clarifies your requirements, proposes the right mode, and matches the process to the stakes.

---

## The Problem This Solves

AI jumps to execution. It assumes what "active" means, picks a metric, chooses a date field, and writes code — all before you've agreed on what question you're actually answering.

For quick questions, that's fine. For analysis that drives a decision, it's a risk.

This skill calibrates the process to what the question actually requires. Fast questions get a fast answer. Rigorous questions get a gate-driven process with a human-in-loop, where every material assumption is surfaced, agreed, and recorded.

---

## Two Modes

### 🔍 Data Scout
Fast, visual, conversation-driven. Attach a file, ask a question, get a dashboard and findings in minutes.

```
Attach file → Confirm 1–3 ambiguities → Dashboard + bullets
                                               ↓
                              2–3 unsolicited observations
                                               ↓
                         "Here's what this hints at if you want to go deeper..."
```

No sign-off blocks. Output and scripts lives in the scout/ and can be reused when the dataset gets updated (e.g. weekly sales data)

**Use for:** EDA, quick metric lookups, "what's in this data?" questions, one-off descriptive questions.
Scout provides high-level insights and also suggests ideas for leveraging the data for driving business outcomes, seamlessly handing off to the Data Scientist agent.
User can also consult, brainstorm and iterate the analysis with scout.

### 🔬 Data Scientist
Rigorous, gate-driven, reproducible. Built on the [CRISP-DM framework](https://www.datascience-pm.com/crisp-dm-2/). Every step produces a signed-off artifact. No step begins until the requirements and prior gate is approved.

```
Business Understanding → Data Understanding → Analysis Plan
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

**Independent Adversarial review happens twice** — after the analysis plan (catches design flaws before any code runs) and before the final report (catches unsupported claims). The reviewer reads artefacts only; it never re-runs analysis. This replicates a typical peer or expert council review to catch any defects in the plan.

**The retrospective is proactive.** After every project, the AI prompts a 5-minute review before you close. Learnings feed back into the skill itself.

**Use for:** causal studies, incrementality, A/B analysis, attribution, churn prediction, ML models, statistical forecasting, scoring, and any analysis that drives or automates a decision.

### The agent proposes — you decide

The agent reads your request and proposes a mode using two questions: *Will this drive or automate a decision? Is there a causal or predictive claim?* If yes to either → Data Scientist. Otherwise → Data Scout, with an offer to escalate.

You can always override: say *"switch modes"* at any point.

---

## Expertise Levels (Both Modes)

| Level | Who it's for |
|---|---|
| **Guided** | Non-specialists or first-time users. AI fills in reasonable defaults, explains every choice in plain language, asks 1–2 questions at a time. Inferred from how the user writes. |
| **Collaborative** | Practitioners who want to stay in control. AI presents options with tradeoffs and a recommended path. Default. |
| **Expert** | Experienced data scientists. Concise, technical, reproducibility and review gates active by default. |

The agent infers the level from the conversation — you don't need to declare it.

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
    data-ingestion-and-access.md  ← reading Excel, CSV, databases, MCP sources
    harness-config.md              ← per-harness model config + adversarial reviewer prompt
    self-improvement.md            ← retrospective protocol and contribution guide
    benchmark-tests.md             ← test cases for evaluating skill quality
```

Each reference file is loaded on demand — only when the current phase needs it. The main `SKILL.md` stays in context throughout.

---

## Recommended Setup — Harness and MCPs

This skill is designed for a modern AI coding harness (Claude Code, OpenCode, or Codex CLI) connected to the tools your team already uses. The harness handles model routing and agent invocation. The MCPs extend what the agent can see and do.

### Harness

| Harness | Notes |
|---|---|
| **OpenCode** | Full support — per-agent model config, local agent file for adversarial reviewer, MCP connections |
| **Claude Code** | Full support — subagent model config via frontmatter, MCP connections |
| **Codex CLI** | Supported — single model per session; adversarial review runs in a separate session |

See `references/harness-config.md` for setup instructions for each.

### Knowledge Base MCPs (connect these for richer context)

At project start the agent searches connected knowledge tools before asking clarifying questions — pulling existing metric definitions, prior analyses, and data dictionaries so you don't have to retype context you already have.

Useful knowledge MCPs to connect: **Notion, Confluence, Google Drive, SharePoint**, or any internal documentation server your team uses.

### Data MCPs (extension point)

Connecting the agent to live data sources is a powerful extension but requires environment-specific configuration — permissions, query costs, and schema conventions vary. The core skill works with files you attach (CSV, Excel, data exports). Data connector support is an extension point for your fork.

When you're ready to add it, the provenance and validation standards in `references/data-ingestion-and-access.md` define the contract any connector must meet.

Data sources worth connecting in your environment: **Snowflake, BigQuery, Redshift, Databricks, dbt, Looker, Mode, Athena**.

---

## Easiest way to getting started

Start a new project on Codex, Claude Code, OpenCode or Cowork, and say:
*"Install the skill locally (or globally) from https://github.com/ars-codelab/my-datascience-agent-skill "*

## Starting a new analysis
After the skill is installed, restart the session and say: *"Run the data science skill. Here's my question: [your business question]."*


## For adversarial reviewer setup (recommended)

Follow the per-project agent file instructions in `references/harness-config.md`.
Multi agent setups are harness dependent.
For fall back, open a separate session. Switch to a stronger model and pass it the artefact package manually. See `references/harness-config.md`.

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

**Match the process to the stakes.** A T4W revenue question doesn't need a gate document. A churn model does. The agent proposes the right mode rather than defaulting to maximum rigour or minimum effort.

**Clarify before assuming.** Undefined terms — `active`, `churned`, `conversion`, `high value` — are never silently translated into code. In Scout mode: 1–3 inline questions, only the ones that would break the answer. In Scientist mode: full clarification at each gate.

**The agent has a voice.** When the data can't support the question, the agent says so — specifically, calmly, and with an honest alternative. Silently computing a weaker answer without flagging the limitation is the failure mode this skill is designed to prevent.

**Gate before executing (Scientist mode).** No data exploration until business context is signed off. No feature engineering until data understanding is signed off. No report writing until adversarial review is clean.

**Artefacts are decision surfaces, not data dumps.** The business context document is 300–500 words. The analysis plan is 600–900 words. Detailed evidence lives in `outputs/` — the gated artefacts stay readable.

**Adversarial review is scoped, not redundant.** The reviewer gets a read-only artefact package. It challenges and returns structured findings. It does not re-run analysis. The expensive model is used for thinking, not for repeating work.

**Scout always bridges to Scientist.** Every Scout output closes with observations the data raised that would take a proper analysis to answer. The escalation is earned by the data, not manufactured.

**The retrospective closes the loop.** Learnings don't disappear into chat history. They're written to files that persist, accumulate, and feed back into the skill.

---

## Contributing

PRs welcome at [github.com/ars-codelab/my-datascience-agent-skill](https://github.com/ars-codelab/my-datascience-agent-skill). One change per PR. Include the retrospective note that motivated the change and describe what was broken without it.

Tag your PR: `mode:guided` · `mode:expert` · `gate` · `question-quality` · `harness` · `scientific-guardrail` · `self-improvement`

Open an issue first for anything that touches the non-negotiable rules or gate structure — those changes benefit from discussion before implementation.

---

## License

MIT
