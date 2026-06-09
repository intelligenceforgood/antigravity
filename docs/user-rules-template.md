# Recommended user_rules Additions for Antigravity

These rules should be added to each team member's `user_rules` configuration in Antigravity settings to ensure consistent agent behavior across the team.

## How to Add

1. Open a new chat with Antigravity.
2. Copy the entire text from the `Recommended Rules` block below.
3. Paste it into the chat with the message: **"Please remember the following rules globally for all future conversations:"**
4. Antigravity will automatically extract and save these to its implicit memory, persisting them across all sessions.

---

## Recommended Rules

```text
## I4G Skill Rules

### Skill Awareness
- The I4G team uses standardized skills natively installed in the `.agents/skills/` directory. When asked to plan work, fix bugs, review code, merge, deploy, or perform other SDLC activities, check if a relevant skill exists and execute it autonomously.
- The skill catalog is at `antigravity/docs/skill-catalog.md`. Consult it when unsure which skill applies.

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

### Model Routing & Token Economy
- Route planning, design, and architecture skills (`/prd`, `/arch`, `/plan-work`) strictly to the high-reasoning **Opus 4.6** model. (Do not use Gemini Pro for architecture).
- Route code review and verification (`/code-review`) to the **Sonnet 4.6** model.
- Route execution, coding, and diagnostic tasks to cost-effective Gemini models:
  - **Gemini 3.5 Flash (H/M)** for daily coding, test suites, and bug-fixing.
  - **Gemini 3.5 Flash (Low)** for simple script updates, log reading, and running command lines.
  - **Gemini 3.1 Pro (H/L)** for complex troubleshooting, trace logs, or multi-repo integrations.
- Practice token conservation: prefer targeted edits using search-and-replace (`replace_file_content`) over re-writing large sections of files. Avoid reading entire files if search/grep can find the context.
```

---

## Notes

- These rules are **recommendations**, not auto-injected. Each developer adds them to their own Antigravity settings.
- The rules complement (not replace) the detailed knowledge files in `antigravity/knowledge/`.
- As the team discovers new patterns, update this template and notify the team to sync their rules.
