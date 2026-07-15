# Harness Configuration

Use this reference when setting up reviewer models or adapting the skill to a specific agent harness.

## Principle

The skill is portable Markdown. Harnesses differ in whether they support native subagents, named agent profiles, per-agent model selection, structured-choice UI, and persistent local configuration. Use the strongest native mechanism available, and record what was actually used.

Do not assume model names are portable. Ask the user to choose from models available in the current harness.

## First-Run Reviewer Setup

At the start of a project, ask:

```text
Which models should I use for independent review roles in this analysis?

A. Use the strongest available model for both reviewers. (Recommended)
B. Use a cheaper working model, strongest model for both reviewers.
C. Use different models: one for adversarial_test and one for final_business_review.
D. Same model as the working agent; label reviews as same-model fallback.
E. Something else: [your preference].
```

If the user chooses C, ask for:

| Role | User-selected model | Harness mechanism | Fallback |
|---|---|---|---|
| `adversarial_test` |  |  |  |
| `final_business_review` |  |  |  |

Record the result in `00_PROJECT_LOG.md`.

## Harness Notes

### OpenCode

Use OpenCode's native agent/model profile mechanism when available. Configure or select two reviewer profiles:

- `adversarial_test`: strongest reasoning model available.
- `final_business_review`: strongest reasoning + writing model available.

If OpenCode exposes structured choices, use them for the first-run setup. If OpenCode only supports plain text, present lettered choices and record the selected model names in `00_PROJECT_LOG.md`.

### Codex

Use Codex's available subagent or multi-agent tools when present. If a separate reviewer agent can be spawned, pass it only the analysis folder and the review prompt. Do not pass the working agent's private reasoning or intended conclusion.

If Codex cannot select a different model for the reviewer, still run an independent-context review when possible and label the result `same-model reviewer` or `single-agent fallback`.

### Claude Code

Use Claude Code's native subagent or agent-profile support when available. Prefer a stronger model for `final_business_review` than the working agent when cost permits.

If Claude Code uses repository or user-level agent files, keep reviewer role prompts separate from the main workflow instructions. The reviewer should inspect artifacts from disk and return findings; the working agent must make corrections and update stale artifacts.

### Other Harnesses

Map the same two roles to whatever the harness provides:

- Native subagent with chosen model: use it.
- Named agent profile but no model choice: use the profile and record the model as unknown or inherited.
- No subagents but model choice exists: run a fresh review turn with the chosen model if possible.
- No subagents and no model choice: use single-agent fallback and label it clearly.

## Required Record In `00_PROJECT_LOG.md`

```markdown
## Reviewer Configuration

| Role | Harness | Model / profile | Independence status | Fallback rule |
|---|---|---|---|---|
| adversarial_test |  |  | independent / same-model / fallback |  |
| final_business_review |  |  | independent / same-model / fallback |  |
```

