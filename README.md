# An AI Data Science Agent for Knowledge Workers

Most business teams don't have a dedicated data scientist. In markets like Japan, top-tier data talent is genuinely scarce. Meanwhile, the decisions that need analytical rigour — forecasting, causal inference, incrementality, churn prediction — don't wait for headcount.

This is a personal data science agent for knowledge workers: PMs, marketers, supply chain specialists, and anyone who needs to go deeper than a dashboard but doesn't have a data scientist on call. It guides you through the full analytical process, clarifies before assuming, and involves you at the right moments — not constantly, not never.

> Built on five principles: systematic process, human-in-the-loop, domain-first context, adversarial peer review, and self-improvement. The same principles you'd apply to onboarding a human analyst.

---

## The Problem

AI jumps to execution. It assumes what "active" means, picks a metric, chooses a date field, and writes code — all before you've agreed on what question you're answering. For quick questions, that's fine. For analysis that drives a decision, it's a risk.

Two problems show up consistently:

**Unchecked enthusiasm.** Ask "why did my traffic drop?" and the LLM immediately makes unverified assumptions, writes hundreds of lines of code, and outputs a convincing story. Reviewing the reasoning and execution chain takes time. Most users trust the first output.

**The evaluation problem.** Complex questions like "how much inventory should we stock?" don't have a verifiable ground truth. The answer depends on assumptions, and trust has to be earned through backtesting and real-world validation. How do you evaluate AI outputs when there's no answer key?

These are the same challenges you face when onboarding a human analyst. We de-risk human work through structure, coaching, peer review, and validation. This agent applies the same principles to AI.

---

## Two Modes

The agent reads your request and proposes the right mode. You can always override.

### 🔍 Data Scout
Fast, visual, conversation-driven. Attach a file, ask a question, get a dashboard and findings in minutes. No gate documents, no sign-off blocks.

```
Attach file → Confirm 1–3 ambiguities → Dashboard + findings
                        ↓
           2–3 unsolicited observations
                        ↓
     "Here's what this hints at if you want to go deeper..."
```

Scripts live in `scout/code/` and can be rerun when the dataset updates — point them at next week's file and the dashboard refreshes. Scout also brainstorms ideas for how the data could drive business outcomes, and hands off seamlessly to Data Scientist when the question warrants it.

**Use for:** EDA, quick metric lookups, "what's in this data?", one-off descriptive questions.

### 🔬 Data Scientist
Rigorous, gate-driven, reproducible. Built on [CRISP-DM](https://www.datascience-pm.com/crisp-dm-2/). Every step produces a signed-off artefact. No step begins until the prior gate is approved.

```
Business Understanding → Data Understanding → Analysis Plan
         ↓                       ↓                  ↓
    [Sign-off]             [Sign-off]    [Sign-off + Design Review]
                                                    ↓
                                Data Prep → Analysis → Validation
                                                    ↓
                                          [Results Review]
                                                    ↓
                                            Final Report
                                                    ↓
                                     Retrospective ← triggered automatically
```

**Adversarial review happens twice** — after the analysis plan (before any code runs) and before the final report (before any claims are published). An independent agent with a stronger model challenges the design and findings. This replicates a peer or expert council review. The reviewer reads artefacts only; it never re-runs analysis.

**The retrospective is proactive.** After every project, the agent prompts a short review. Learnings feed back into the skill itself.

**Use for:** causal studies, incrementality, A/B analysis, attribution, churn prediction, ML models, statistical forecasting, scoring, and any analysis that drives or automates a decision.

### How the agent decides

Two questions: *Will this output drive or automate a decision? Is there a causal or predictive claim?* Yes to either → Data Scientist. Otherwise → Data Scout, with a bridge offer to go deeper.

---

## Five Design Principles

| | Principle | What it prevents |
|---|---|---|
| ⚙️ | **Systematic process** | Follows CRISP-DM. Clarifies requirements, validates data, and secures sign-off at each stage before writing code. | AI executing on a poorly framed question |
| 🧠 | **Human-in-the-loop** | Pauses at gates where business judgment is required. Adapts to your technical level via three expertise modes. | Both extremes: constant interruption and full autonomy |
| 📚 | **Domain-first context** | Searches your knowledge base and past analyses before asking questions. Context before computation. | Technically correct answers to the wrong question |
| 🥊 | **Adversarial peer review** | Invokes an independent agent with a stronger model at two checkpoints. Its job is to falsify, not approve. | Leaky features, unsupported claims, overconfident results |
| 🔄 | **Self-improvement** | Proactive retrospective after every project. Learnings written back into the skill. | Repeating the same mistakes across projects |

---

## Expertise Levels

The agent infers your level from the conversation. No need to declare it.

| Level | Who it's for | How it behaves |
|---|---|---|
| **Guided** | Non-specialists, first-time users | Plain language, fills in sensible defaults, 1–2 questions at a time |
| **Collaborative** | Practitioners who want to stay in control | Options with tradeoffs, recommended path, invitation to push back. Default. |
| **Expert** | Experienced data scientists | Concise, technical, reproducibility and review gates fully active |

---

## Multi-Model Architecture (Optional)

Spend more on thinking, less on execution. Assign different models to different roles when your harness supports it.

| Role | Phase | Suggested tier |
|---|---|---|
| **Planner** | Business context, data understanding, analysis plan | Strongest available |
| **Executor** | Data prep, analysis, validation | Mid-tier |
| **Adversarial Reviewer** | Design review + results review | Strongest available |

Fully supported on Claude Code and OpenCode. Codex CLI falls back to a separate session for adversarial review. See `references/harness-config.md` for setup.

---

## Getting Started

This agent is designed for modern AI coding harnesses that support local file creation. Start a new project in Codex, Claude Code, OpenCode, or Cowork and say:

> *"Install the skill from https://github.com/ars-codelab/my-datascience-agent-skill"*

Then restart the session and say:

> *"Run the data science skill. Here's my question: [your business question]."*

For adversarial reviewer setup, follow the per-project agent file instructions in `references/harness-config.md`. For a quick fallback, open a separate session, switch to a stronger model, and pass it the artefact package manually.

---

## Connecting Your Stack

### Knowledge Base MCPs (recommended)

The agent searches connected knowledge tools at project start — pulling existing metric definitions, prior analyses, and data dictionaries before asking you to retype them.

Connect: **Notion, Confluence, Google Drive, SharePoint**, or any internal documentation server.

### Data MCPs (extension point)

Data connector support requires environment-specific configuration and is an extension point for your fork. The core skill works with files you attach (CSV, Excel, exports). When you're ready to extend it, `references/data-ingestion-and-access.md` defines the standards any connector must meet.

Worth connecting in your environment: **Snowflake, BigQuery, Redshift, Databricks, dbt, Looker, Mode, Athena**.

---

## What Gets Created Per Project

Folders are created lazily — only when a file is actually written there.

```
analysis_slug/
  scout/
    code/                    ← reusable Python scripts, one per Scout run
    output/                  ← date-stamped HTML dashboards
  00_PROJECT_LOG.md          ← mode, model config, gate waivers
  01_BUSINESS_CONTEXT.md     ← decision, KPI, scope [sign-off required]
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

After every project, the agent proactively prompts a short retrospective. Findings go to two places:

- **`memory/reusable_context.md`** — project-local. Approved KPI definitions, field semantics, data quality patterns. Reused in future analyses.
- **`memory/skill_improvement_notes.md`** — accumulates across projects. Process gaps, better question phrasing, missing guardrails. Your backlog for improving the skill.

### Fork → improve → contribute

```
Fork → run projects → collect notes in skill_improvement_notes.md
              ↓
  Edit your local SKILL.md or references/ based on learnings
              ↓
         Test on the next project
              ↓
  If it generalises → open a PR
```

**Keep in your fork:** domain-specific KPI definitions, org-specific data quality patterns, harness config for your setup.

**Worth a PR:** fixes to ambiguous questions, missing scientific guardrails, gate calibration improvements, Guided mode improvements, new harness support.

See `references/self-improvement.md` for the full contribution guide and PR tagging conventions.

---

## Contributing

PRs welcome at [github.com/ars-codelab/my-datascience-agent-skill](https://github.com/ars-codelab/my-datascience-agent-skill). One change per PR. Include the retrospective note that motivated the change and describe what was broken without it.

Tag your PR: `mode:guided` · `mode:expert` · `gate` · `question-quality` · `harness` · `scientific-guardrail` · `self-improvement`

Open an issue first for anything that touches the gate structure or non-negotiable rules.

---

## License

MIT · Anoj Sundar · Framework and Engineering Design
