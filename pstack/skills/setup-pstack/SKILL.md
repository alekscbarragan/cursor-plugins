---
name: setup-pstack
description: Configure which models pstack uses per role. Detects your available models and writes an always-applied rule that overrides the skill defaults. Use for /setup-pstack, "configure pstack models", or changing pstack's model choices.
---

# Setup pstack

Write `~/.cursor/rules/pstack-models.mdc`, an always-applied rule that sets pstack's model per role. The skills read it and fall back to their inline defaults when a line is absent, so this is an override layer, not a requirement.

## Steps

### 1. Detect available models

Enumerate the model slugs you can pass to a `Task` subagent in this session; that is the dependable source. If Cursor also exposes a models API or CLI that lists the user's entitled models, prefer it for completeness. If you cannot detect any, ask the user to paste the slugs they have access to. Never write a slug you have not confirmed is available.

### 2. Load current state

The default role-to-model mapping is the rule shape shown in step 5 below. If `~/.cursor/rules/pstack-models.mdc` already exists, read it and treat its values as the current choices. Otherwise start from those defaults.

### 3. Map and confirm

Show every role with its current model, marking any whose model is not in the detected set as needing a choice. Ask whether to accept as-is or change specific roles, offering the detected models as the options. Prefer AskQuestion over free text. For panel roles (how critics, arena runners, architect runners, interrogate reviewers) the value is a list, and one subagent runs per model, so the list length sets the count. `arena cross-judge pool` is also a list, but Arena selects one model from it whose family differs from the parent's when possible. Other judge seats (eval blinded judge, figure-it-out judge) must be a different model family from the generators they score — flag same-family collisions before writing.

### 4. Validate

Every slug written must be in the detected set. If a chosen slug is not available, stop and ask again. A rule pointing at a model the user cannot use breaks every delegation that reads it.

### 5. Write the rule

Write `~/.cursor/rules/pstack-models.mdc` with `alwaysApply: true` and one line per role, using the same labels poteto-mode uses. Overwrite the whole file so re-runs stay idempotent. Shape:

```
---
description: pstack per-role model choices (overrides skill defaults)
alwaysApply: true
---
# pstack model configuration. One line per role. Delete a line to fall back to the skill default.
feature, refactoring: composer-2.5
bug-fix: gpt-5.5-high-fast
perf-issue: gpt-5.5-high-fast
hillclimb: gpt-5.5-high-fast
judgment and prose: claude-fable-5-medium-thinking
hardest tasks: claude-fable-5-thinking-max
how explorer: composer-2.5
how explainer: claude-fable-5-medium-thinking
how critics: claude-fable-5-medium-thinking, gpt-5.5-high-fast, composer-2.5
why investigators: composer-2.5
why synthesizer: claude-fable-5-medium-thinking
reflect tooling: composer-2.5
reflect judgment, divergent, synthesizer: claude-fable-5-medium-thinking
arena runners: gpt-5.5-high-fast, composer-2.5, grok-4.5-xhigh
arena cross-judge pool: claude-fable-5-medium-thinking, gpt-5.5-high-fast, composer-2.5
architect runners: gpt-5.5-high-fast, composer-2.5, grok-4.5-xhigh
interrogate reviewers: claude-fable-5-medium-thinking, gpt-5.5-high-fast, composer-2.5
figure-it-out delegate: gpt-5.5-high-fast
figure-it-out judge: claude-fable-5-medium-thinking
```

Add `# fallback: claude-opus-4-8-thinking-high` on judgment roles; J3 E3 uses `claude-fable-5-low-thinking` per dispatch-rubric overlay. Arena/architect runners exclude the judgment family so the cross-judge seat stays independent.

### 6. Confirm

Tell the user the rule was written and that it applies to new sessions. Re-running this skill updates it.

## Per-runtime siblings

This skill configures the Cursor rule only. The same role labels ship as per-runtime projections of the `dispatch-rubric-v2.md` role matrix (see its "pstack role crosswalk"), installed by `~/.ai/setup.sh`:

- `~/.ai/rules/pstack-models-cc.md` — Claude Code, always-on via `~/.claude/rules/` (models `sonnet`/`opus`/`fable`; effort via `cc-agent-*` bins)
- `~/.ai/rules/pstack-models-codex.md` — Codex, managed marker block in `~/.codex/AGENTS.md` (gpt-5.6-sol, roles differ by reasoning effort)
- `~/.ai/rules/pstack-models-relays.md` — on-demand, kokoro-mode "with relays" override (cross-provider relay seats)
- `~/.ai/cursor/pstack-models.mdc` — the tracked template behind `~/.cursor/rules/pstack-models.mdc`

**Write-back rule:** `setup.sh` regenerates `~/.cursor/rules/pstack-models.mdc` from the `~/.ai` template on every run. After step 5, copy the written rule back to `~/.ai/cursor/pstack-models.mdc` (when that repo exists on the machine) — otherwise the next `setup.sh` run reverts your choices. When a change reflects a policy shift rather than slug availability, update the matrix and the sibling tables together.

### 7. Offer a verification skill (optional)

Check whether the project has a way to drive the real app for proof (a `verify-*` skill, or an existing harness). If not, offer once: "want a project-local verification skill, so agents can drive the app the way a user does and prove changes work? I can generate one with /create-verification-skill." On yes, invoke `/create-verification-skill` (resolves wherever pstack is installed — workspace, user, or plugin). On no, move on without pushing.
