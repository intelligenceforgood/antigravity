# Recommended user_rules Additions for Antigravity

These rules should be added to each team member's `user_rules` configuration in Antigravity settings to ensure consistent agent behavior across the team.

## How to Add

In Antigravity settings, add the following to your global user rules. These persist across ALL conversations.

---

## Recommended Rules

```text
## I4G Workflow Rules

### Workflow Awareness
- The I4G team uses standardized workflows defined in `antigravity/workflows/`. When asked to plan work, fix bugs, review code, merge, deploy, or perform other SDLC activities, check if a relevant workflow exists and follow its procedure.
- The workflow index is at `antigravity/workflows/INDEX.md`. Consult it when unsure which workflow applies.

### Knowledge Loading
- Before writing or modifying code in any I4G repository, proactively read the relevant standards from `antigravity/knowledge/standards/` (e.g., `python.md` for Python files, `typescript.md` for TypeScript/React files).
- Before planning or architecting features, proactively read `antigravity/knowledge/architecture/architecture.md`.
- When encountering unfamiliar patterns or making risky changes, check `antigravity/knowledge/operational/lessons-learned.md` for known pitfalls.

### Output Discipline
- Never output full file contents or large code diffs in chat responses. Since files are already updated via tools, simply list the modified files.
- Do NOT output the contents of plan documents or changelogs in chat. Confirm they were updated and summarize key points.

### Planning Discipline
- When working from a task plan (in `planning/tasks/`), always check off completed tasks by changing `[ ]` to `[x]`.
- Record significant changes in `planning/change_log.md` with dates.
- When blocked by ambiguity, stop work and produce a structured clarification request rather than guessing.

### Multi-Repo Consistency
- The I4G platform spans multiple repos (core, ui, ssi, infra, ml, etc.) that must stay in sync.
- When changes span multiple repos, they must be committed and pushed together.
- After any merge, verify ALL repos have clean working trees, not just the ones you changed.

### Coding Standards
- Python: Type hints on all functions, Google-style docstrings on public methods, settings via `get_settings()`, stores via factories.
- TypeScript/React: Follow Next.js 15 patterns, await dynamic route params, use the SDK client for API calls.
- Always run relevant tests before considering a task complete.
```

---

## Notes

- These rules are **recommendations**, not auto-injected. Each developer adds them to their own Antigravity settings.
- The rules complement (not replace) the detailed knowledge files in `antigravity/knowledge/`.
- As the team discovers new patterns, update this template and notify the team to sync their rules.
