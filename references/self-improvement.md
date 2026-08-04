# Self-Improvement Reference

This reference covers two things: how to run the post-project retrospective, and how to turn retrospective output into skill improvements.

---

## Retrospective Protocol

### When to trigger

Immediately after the final report is accepted or the user signals the project is done. Do not wait to be asked. Surface this prompt:

> "Before we close — three quick questions that'll make the next analysis faster. Takes 5 minutes and feeds directly into improving this skill."

If the user defers, note it in `00_PROJECT_LOG.md` as `Retrospective: deferred — [reason]` and do not nag.

### Questions to ask

Keep to 3–5 questions. Adapt based on what happened in the project. Default set:

**1. What slowed us down?**
> Was there a clarification loop that took longer than expected? A data quality issue we didn't anticipate? A gate we had to revisit?

**2. What assumption turned out to be wrong?**
> Did an approved definition need revision mid-project? Did a field mean something different from what was documented?

**3. What would you do differently next time?**
> Different framing of the business question? Different data check? Different validation approach?

**4. What worked well that we should repeat?**
> A question format, a profiling approach, a presentation structure?

**5. Is there anything in this analysis that would be useful as a template or reusable pattern?**
> A cleaning approach, a metric definition, a scorecard structure?

Guided mode: ask these one at a time in plain language, with examples.
Collaborative/Expert mode: ask as a compact set, invite freeform answers.

### What to do with the answers

**Write to `memory/reusable_context.md`** (project-local):
- Approved KPI definitions, population rules, field semantics specific to this domain
- Data quality patterns and workarounds that applied here
- Validated analytical approaches for this business context

**Write to `memory/skill_improvement_notes.md`** (accumulates across projects):
- Process gaps: a question the skill should always ask but didn't
- Better default options for a clarification question
- A gate that was too strict or not strict enough for this type of work
- Phrasing that confused the user in a given mode
- A pattern that should become a reusable template

Format each note in `skill_improvement_notes.md` as:

```markdown
## [Date] — [Project slug]

**What happened:** [1–2 sentences]
**Proposed change:** [What should be different in the skill]
**Generalizable?** Yes / Probably / Domain-specific
**Suggested location:** SKILL.md / artifact-templates.md / harness-config.md / question-banks
```

---

## Skill Contribution Guide

This skill is designed to be forked and improved locally. The improvement loop:

```
Your project → Retrospective → skill_improvement_notes.md
     ↓
Review notes periodically → decide what's generalizable
     ↓
Edit your local SKILL.md or reference files
     ↓
Test on the next project
     ↓
If it holds up → file a PR to the main skill repo
```

### Local improvements (keep in your fork)

- Domain-specific KPI definitions and population rules
- Organisation-specific data quality patterns
- Question options calibrated to your team's vocabulary
- Harness-specific configuration for your setup

### Generalizable improvements (worth a PR)

A change is worth proposing upstream if:
- It fixes a gap that would affect most data science projects, not just yours
- It improves a question that was ambiguous regardless of domain
- It adds a scientific guardrail that's missing from the current skill
- It reduces unnecessary friction (e.g. a gate that was too strict for low-stakes work)
- It improves Guided mode for less experienced users

### What makes a good PR

- One change per PR — easier to review and revert
- Include the retrospective note that motivated the change
- Describe what went wrong without the change and what's better with it
- If it affects a gate or a non-negotiable rule, note the tradeoff explicitly
- Tag it: `mode:guided` / `mode:expert` / `gate` / `question-quality` / `harness` / `scientific-guardrail`

### What stays in `skill_improvement_notes.md`

Everything, including changes you don't PR. This file is your institutional memory of what the skill has been through and why your fork diverges from the main. Review it every few projects.
