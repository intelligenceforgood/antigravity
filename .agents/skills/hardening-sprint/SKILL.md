---
name: Hardening Sprint
description: Bootstrap a platform hardening session from the review/execution plan
---

# Hardening Sprint

**Role: Autonomous Agent.** Start a platform hardening work session. Load the review, execution plan, and guide you to the next task.

## When to Use This Skill

- Running through a platform review backlog or hardening plan
- The user says "hardening sprint", "start hardening", or "platform review"
- A review document with prioritized findings exists in `planning/tasks/`

## Steps

1. **Load the plan and execution state.** The user may specify a plan directly, or you should locate it:
   - Search `planning/tasks/` for files matching `*hardening*`, `*review*`, or `*sprint*`.
   - If multiple candidates exist, list them and ask the user to confirm.
   - The plan must contain `- [ ]` checkboxes for progress tracking.
   - If both a review document (findings) and an execution plan (checkboxes) exist, load both.

2. **Identify progress.** Count checked (`- [x]`) vs unchecked (`- [ ]`) tasks. Report:
   - Current phase (the earliest phase with unchecked tasks)
   - Next unchecked task
   - Any blocked tasks (dependencies not yet complete)
   - Updated progress table

3. **Load relevant context.** Based on the next task's repo(s), read:
   - The relevant source files referenced in the review finding
   - The relevant standards from `antigravity/knowledge/standards/`
   - Lessons learned from `antigravity/knowledge/operational/lessons-learned.md`

4. **Present the work plan.** Show:
   - Task ID, title, repo, effort
   - The specific finding from the review (with file references)
   - Acceptance criteria from the execution plan
   - Dependencies (met or unmet)
   - Suggested implementation approach

5. **Confirm and execute.** Ask the user to confirm, then follow the `/work-on-task` skill pattern: implement → test → document → validate → summarize.

6. **After completion.** Check off the task in the execution plan, update the progress table, and note the change in `planning/change_log.md`. Do NOT output modified code in chat — just list modified files.
