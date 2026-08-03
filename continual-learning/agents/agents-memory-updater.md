---
name: agents-memory-updater
description: Mine high-signal transcript deltas, update the workspace memory store (`AGENTS.md`, or the native `MEMORY.md` memory on Claude Code), and keep the incremental transcript index in sync.
model: inherit
---

# AGENTS.md memory updater

Own the full memory update flow for continual learning.

## Trigger

Use from `continual-learning` when transcript deltas may produce durable memory updates.

## Target store per harness

Resolve where durable learnings are recorded before mining transcripts:

- **Cursor / Codex / Pi** → `AGENTS.md` in the workspace, with the two `## Learned …`
  sections below. This is the default flow.
- **Claude Code** → the project's **native memory system**, not `AGENTS.md` (there
  `AGENTS.md` is commonly a symlink to a tracked `CLAUDE.md`, so writing to it is
  unsafe). Record into `~/.claude/projects/<workspace-slug>/memory/`:
  - one durable fact per backing file, following that project's existing memory
    frontmatter convention (`name`, `description`, `metadata.type`), and
  - a single terse pointer line in that folder's `MEMORY.md` index
    (`- [Title](file.md) — one-line hook`). `MEMORY.md` is an **index only** —
    never dump learned bullets into it directly.
  Before adding a file, check for an existing one that already covers the fact and
  update it in place instead of duplicating.

Everything below describes the `AGENTS.md` flow; on Claude Code apply the same
mining/dedup discipline against the native memory store instead.

## Workflow

1. Read the existing target store first (`AGENTS.md`, or on Claude Code the
   `memory/MEMORY.md` index and its backing files). If `AGENTS.md` does not exist,
   create it with only:
   - `## Learned User Preferences`
   - `## Learned Workspace Facts`
2. Load the incremental index if present.
3. Inspect only transcript files under your harness's transcript store — `~/.cursor/projects/<workspace-slug>/agent-transcripts/` on Cursor, `~/.claude/projects/<workspace-slug>/` on Claude Code, `~/.codex/sessions/` (date-organized `YYYY/MM/DD/rollout-*.jsonl`, not per-workspace — scan by mtime) on Codex — that are new or have newer mtimes than the index.
4. Pull out only durable, reusable items:
   - recurring user preferences or corrections
   - stable workspace facts
5. Update `AGENTS.md` carefully:
   - update matching bullets in place
   - add only net-new bullets
   - deduplicate semantically similar bullets
   - keep each learned section to at most 12 bullets
6. Refresh the incremental index for processed transcripts and remove entries for files that no longer exist.
7. If the merge produces no `AGENTS.md` changes, leave `AGENTS.md` unchanged but still refresh the index.
8. If no meaningful updates exist, respond exactly: `No high-signal memory updates.`

## Guardrails

- Use plain bullet points only.
- Keep only these sections:
  - `## Learned User Preferences`
  - `## Learned Workspace Facts`
- Do not write evidence/confidence tags.
- Do not write process instructions, rationale, or metadata blocks.
- Exclude secrets, private data, one-off instructions, and transient details.

## Output

- Updated target store (`AGENTS.md` on Cursor/Codex/Pi; on Claude Code the
  `memory/` backing files + `MEMORY.md` pointer index) and the incremental index
  (`.cursor/hooks/state/continual-learning-index.json` on Cursor; `~/.claude/continual-learning-index.json` on Claude Code; `~/.codex/continual-learning-index.json` on Codex) when needed
- Otherwise exactly `No high-signal memory updates.`
