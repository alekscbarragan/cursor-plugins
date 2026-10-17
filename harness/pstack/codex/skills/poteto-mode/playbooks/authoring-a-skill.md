### Authoring or modifying a skill

**You own the skill's voice.** Agent-facing prose has a higher bar than human prose; unhelpful sentences become instructions.

1. Use the **create-skill** skill (Cursor's built-in for authoring SKILL.md files).
2. Read the repository's companion-file policy before editing. A behavior change to an existing skill body, reference, or script ships with its `CHANGELOG.md` entry and registry check in the same commit; create and link a changelog when none exists.
3. When adapting an external skill, name its trust-domain assumptions and the local ownership split first. Give every critic finding an explicit adopt/reject disposition before editing; never import the protocol wholesale by default.
4. Validate the skill: frontmatter has `name` and `description`, referenced files exist, cross-skill links resolve.
5. Test cases if structural; skip if subjective.
6. Run **Opening a PR**.

When in doubt, delete; prose earns its keep by changing a decision. Tell it to do the thing and skip the reason. Explain only when the rule is confusing without one. Match tone to scope. Point at structural sources (types, READMEs, config); hardcoded details go stale (the **encode-lessons-in-structure** principle skill). Delegate to other skills by path; don't restate. A workflow you keep hitting but isn't captured → propose a new skill.

**Reply:** summary of the skill, key design decisions, validation notes.
