---
name: swarm
description: "Swarm N workers on herdr, drain them, return one report. Use for /swarm, 'swarm this', coverage slices, races, or gauntlets."
---

# Swarm

Fan out N parallel workers. Cover separate slices, race the same brief, or mix both. The parent waits, aggregates, and returns one report.

## Open the phase list

Open a todolist with one entry per phase before launching anything.

1. Frame
2. Fan out
3. Aggregate
4. Report

## Phase A: Frame

1. State the done predicate and the artifact or report the swarm must return.
2. Choose the shape: slices, a race on identical briefs, or a mix. For a race or a mix, declare `first pass`, `rank all`, or `best-of` before you launch.
3. Set N from the user, or derive N from the shape. N is the worker count, not a host concurrency cap.
4. Pick the worker model from the `swarm workers` line in this host's pstack-models file. Cursor: `~/.cursor/rules/pstack-models.mdc`. Claude Code: `~/.ai/rules/pstack-models-cc.md`. Codex: the pstack-models marker in `~/.codex/AGENTS.md`. For a model race, name each arm's model up front.
5. Give each worker its own writable output when it writes. Use a worktree, a branch, or `/tmp/swarm-<slug>/worker-<n>/`.

Frame is done when the predicate, the shape, N, the model, and the output paths are named.

## Phase B: Fan out

Call the Skill tool with `fan-out`. Skip fan-out's swarm-by-path bullet. Frame already ran.

Take fan-out's User-asked launch. Launch all N workers in one wave as herdr panes.

If a worker must start from a non-default pushed branch, name that branch in the brief so the worker checks it out.

Write each brief so it stands alone. Include the goal, the scope, the exact slice or race arm, how to verify, and what to report. Reports use `PASS`, `ISSUES`, or `BLOCKED` with evidence.

If a worker drops out, proceed with N-1 and note the dropout.

Fan out is done when N workers have started, or N-1 plus a named dropout.

## Phase C: Aggregate

Read the result files, not pane scrollback. For coverage, every required slice needs a result. For a race, apply the selection rule declared in Frame. Use first pass, rank all, or best-of. Quote evidence. Do not paste raw worker dumps.

Keep a compact result table, one-line evidenced issues, and named gaps or dropouts.

Aggregate is done when every required slice has a row, or the race rule has a winner, and dropouts are named.

## Phase D: Report

Return one in-chat report with the table, the issue one-liners, the gaps or dropouts, and the race rule when a race ran.
