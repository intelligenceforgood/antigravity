# Hardening Sprint

**Role: Autonomous Agent.** Start a platform hardening work session. Load the review, execution plan, and guide you to the next task.

## Steps

1. **Load the plan and execution state.** Read these files:
   - `planning/tasks/platform-review-2026-04-17.md` — the full architecture review (Parts 1–6)
   - `planning/tasks/platform-hardening-execution.md` — the execution plan with checkboxes

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

5. **Confirm and execute.** Ask the user to confirm, then follow the `work-on-task` workflow pattern: implement → test → document → validate → summarize.

6. **After completion.** Check off the task in the execution plan, update the progress table, and note the change in `planning/change_log.md`. Do NOT output modified code in chat — just list modified files.
