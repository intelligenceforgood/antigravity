# Customization Guide

How to extend and maintain the Antigravity Workflow Framework for the I4G team.

---

## Adding a New Workflow

1. **Choose the right tier:**
   - `workflows/planning/` — generates plans, PRDs, or architecture docs
   - `workflows/execution/` — implements code, fixes bugs, or runs tests
   - `workflows/review/` — reviews, audits, or verifies
   - `workflows/lifecycle/` — merges, deploys, or manages project state

2. **Create the markdown file** in the appropriate tier directory:
   ```
   antigravity/workflows/<tier>/my-workflow.md
   ```

3. **Follow the workflow template:**
   ```markdown
   # Workflow Name

   **Role: Autonomous Agent.** Brief description of what this workflow does.

   ## Before You Start
   
   1. Read relevant knowledge from `antigravity/knowledge/standards/`.
   2. [Any other prereqs]

   ## Steps

   1. **Step name.** Description. Use `tool_name` to accomplish X.
   2. ...

   ## Execution Rules

   1. Save output using `write_to_file` to `planning/<subdir>/`.
   2. Do NOT output large text blocks in chat.
   ```

4. **Add to the index** — Update `workflows/INDEX.md` with a new row in the appropriate tier table.

5. **Add to the catalog** — Add a detailed entry in `docs/routine-catalog.md`.

6. **Commit and push** — The workflow is version-controlled and immediately available to all team members.

---

## Updating Knowledge Files

Knowledge files live in `antigravity/knowledge/` and are organized into three categories:

### Architecture (`knowledge/architecture/`)
- **When to update:** When the platform architecture changes (new services, routing changes, database schema updates).
- **Be careful:** These are heavily referenced by workflows. Inaccurate architecture knowledge leads to incorrect code generation.

### Standards (`knowledge/standards/`)
- **When to update:** When coding conventions change, new linting rules are adopted, or framework versions are upgraded.
- **How to update:** Edit the relevant file (e.g., `python.md`, `typescript.md`). Changes take effect in the next conversation.

### Operational (`knowledge/operational/`)
- **When to update:** Continuously — after every session where something new is learned.
- **How to update:** Use the `record-lesson` workflow, or manually add to `lessons-learned.md`.

---

## The Knowledge Promotion Pipeline

Knowledge flows through three stages:

```
Volatile → Persistent → Structural
```

1. **Volatile** (conversation context) — Something learned during a session. Exists only in conversation logs. Easily lost.

2. **Persistent** (`knowledge/operational/lessons-learned.md`) — Captured via the `record-lesson` workflow. Survives across sessions. The agent checks this file when encountering unfamiliar patterns.

3. **Structural** (`knowledge/standards/*.md`) — Promoted from lessons-learned when a pattern becomes established. For example, if 3 separate lessons about the same Python pitfall accumulate, they should be consolidated into `python.md`.

**The `record-lesson` workflow handles promotion automatically** — when a category in `lessons-learned.md` accumulates 3+ similar lessons, the agent promotes the pattern to the appropriate standards file.

---

## Contributing via PR

The `antigravity` repository follows standard Git workflow:

1. **Branch** from `main`.
2. **Make changes** (new workflow, updated knowledge, improved docs).
3. **Test** — invoke the changed workflow in a session to verify it works.
4. **PR** — open a pull request with a clear description of what changed and why.
5. **Review** — at least one team member reviews.
6. **Merge** — squash merge to `main`.

### PR Checklist

- [ ] New workflow added to `workflows/INDEX.md`
- [ ] New workflow added to `docs/routine-catalog.md`
- [ ] Knowledge file changes verified against current codebase
- [ ] No GCA-specific patterns (`@file:`, `applyTo:`, Agent Mode)
- [ ] Workflow uses agent-native patterns (proactive reading, `run_command`, etc.)
- [ ] README updated if directory structure changed

---

## Removing Obsolete Workflows

If a workflow becomes obsolete (like `rehydrate-session` and `prepare-new-session` from GCA):

1. **Delete the file** from `workflows/<tier>/`.
2. **Remove from INDEX.md** and `docs/routine-catalog.md`.
3. **Add a note** to `docs/cookbook.md` if the workflow was commonly used, explaining the replacement approach.
4. **Commit** with message `chore: remove obsolete <workflow-name> workflow`.
