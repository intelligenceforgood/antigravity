---
name: Wrapup and Merge
description: Combined sprint wrapup → code review → merge (the "big button")
---

# Sprint Wrap-Up and Merge

**Role: Autonomous Agent.** Execute the complete end-of-sprint pipeline by chaining the sprint wrap-up and merge skills.

## When to Use This Skill

- End of a work session when changes are ready to be documented, reviewed, and shipped in one step
- The user says "wrapup and merge", "ship everything", or "the big button"
- Preferred over running `/sprint-wrapup` and `/git-merge` separately

## Phase 1 — Sprint Wrap-Up

Read and execute all instructions from the `/sprint-wrapup` skill (`.agents/skills/sprint-wrapup/SKILL.md`) to document the completed work, update tasks, append to the change log, assess risks, and record lessons learned.

## Phase 2 — Merge (including Pre-Merge Review)

Once the sprint wrap-up is complete, read and execute all instructions from the `/git-merge` skill (`.agents/skills/git-merge/SKILL.md`).

*(Note: The merge skill inherently executes the code review procedure, cleans the working tree, and handles committing and pushing.)*
