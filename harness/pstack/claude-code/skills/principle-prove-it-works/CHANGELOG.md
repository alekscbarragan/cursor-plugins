# Prove It Works — changelog

Canonical dated history of `principle-prove-it-works`, newest first; `SKILL.md` points here. Changes before 2026-08-04 are not individually logged.

## 2026-08-04

- **Temporary mirrors are not evidence workspaces** — verification now checks that a tool-managed repository mirror still exists and creates a stable shallow checkout before later tests or subagent reuse depend on it.
