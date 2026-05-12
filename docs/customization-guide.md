# Customization Guide

How to extend and maintain the Antigravity Skill Framework for the I4G team.

---

## Adding a New Skill

1. **Create the skill directory** in the `.agents/skills` folder. Use a descriptive name, optionally prefixing it with its lifecycle category (e.g., `plan-`, `exec-`, `review-`, `lifecycle-`):
   ```
   .agents/skills/my-new-skill/
   ```

2. **Create the SKILL.md file** inside the directory:
   ```
   .agents/skills/my-new-skill/SKILL.md
   ```

3. **Follow the skill template** (which requires YAML frontmatter):
   ```markdown
   ---
   name: My New Skill
   description: Brief description of what this skill does
   ---

   # My New Skill

   **Role: Autonomous Agent.** Detailed description of what this skill does.

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

4. **Add to the catalog** — Add a detailed entry in `docs/skill-catalog.md`.

5. **Commit and push** — The skill is version-controlled and immediately available to all team members natively.

---

## Updating Knowledge Files

Knowledge files live in `antigravity/knowledge/` and are organized into three categories:

### Architecture (`knowledge/architecture/`)
- **When to update:** When the platform architecture changes (new services, routing changes, database schema updates).
- **Be careful:** These are heavily referenced by skills. Inaccurate architecture knowledge leads to incorrect code generation.

### Standards (`knowledge/standards/`)
- **When to update:** When coding conventions change, new linting rules are adopted, or framework versions are upgraded.
- **How to update:** Edit the relevant file (e.g., `python.md`, `typescript.md`). Changes take effect in the next conversation.

### Operational (`knowledge/operational/`)
- **When to update:** Continuously — after every session where something new is learned.
- **How to update:** Use the `/record-lesson` skill, or manually add to `lessons-learned.md`.

---

## The Knowledge Promotion Pipeline

Knowledge flows through three stages:

```
Volatile → Persistent → Structural
```

1. **Volatile** (conversation context) — Something learned during a session. Exists only in conversation logs. Easily lost.

2. **Persistent** (`knowledge/operational/lessons-learned.md`) — Captured via the `/record-lesson` skill. Survives across sessions. The agent checks this file when encountering unfamiliar patterns.

3. **Structural** (`knowledge/standards/*.md`) — Promoted from lessons-learned when a pattern becomes established. For example, if 3 separate lessons about the same Python pitfall accumulate, they should be consolidated into `python.md`.

**The `/record-lesson` skill handles promotion automatically** — when a category in `lessons-learned.md` accumulates 3+ similar lessons, the agent promotes the pattern to the appropriate standards file.

---

## Contributing via PR

The `antigravity` repository follows standard Git workflow:

1. **Branch** from `main`.
2. **Make changes** (new skill, updated knowledge, improved docs).
3. **Test** — invoke the new skill in a session using its `/slash-command` to verify it works.
4. **PR** — open a pull request with a clear description of what changed and why.
5. **Review** — at least one team member reviews.
6. **Merge** — squash merge to `main`.

### PR Checklist

- [ ] New skill added to `docs/skill-catalog.md`
- [ ] Knowledge file changes verified against current codebase
- [ ] No GCA-specific patterns (`@file:`, `applyTo:`, Agent Mode)
- [ ] Skill uses agent-native patterns (proactive reading, `run_command`, etc.)
- [ ] README updated if directory structure changed

---

## Removing Obsolete Skills

If a skill becomes obsolete:

1. **Delete the directory** from `.agents/skills/`.
2. **Remove from the catalog** in `docs/skill-catalog.md`.
3. **Add a note** to `docs/cookbook.md` if the skill was commonly used, explaining the replacement approach.
4. **Commit** with message `chore: remove obsolete <skill-name> skill`.
