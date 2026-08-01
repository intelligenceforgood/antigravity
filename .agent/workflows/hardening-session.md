---
description: Bootstrap a hardening session — load review plan, identify next task, load context, implement with full testing
---

1. Locate the active hardening or review plan. Search `planning/tasks/` across all 9 I4G repos (antigravity, core, ssi, ui, infra, ml, mobile, planning, docs — all at `/Users/jerry/Work/project/i4g/<repo>/`) for files matching `*hardening*`, `*review*`, or `*sprint*`. List every matching file with its full path. If exactly one plan is found, proceed. If multiple plans are found, STOP and present the list to the user so they can choose which plan to work from. If no plan is found, STOP and inform the user.

// turbo

2. Parse progress in the selected plan. Count all `[x]` (completed) vs `[ ]` (unchecked) vs `[/]` (in-progress) tasks. Identify the current phase or section heading. Present a compact progress table: | Phase | Done | Remaining | Total | % |. Include an overall summary line.

// turbo

3. Identify the next unchecked `[ ]` task in the plan. Extract: task ID or number, title/description, target repo(s), effort estimate (if noted), and any dependencies on other tasks. If all tasks are checked, STOP and congratulate the user — the hardening plan is complete.

// turbo

4. Load relevant context for the identified task. Read the source files or modules referenced in the finding. Load relevant standards from `antigravity/knowledge/standards/` (match by topic — e.g., if the task involves API changes load `api-standards.md`, if it involves testing load `testing-standards.md`). Load lessons from `antigravity/knowledge/operational/lessons-learned.md`. Summarize key constraints and patterns that apply to this specific task.

// turbo

5. Present the work plan to the user in a structured format:
   - **Task ID / Title**
   - **Target repo(s)** and affected modules
   - **Effort estimate**
   - **Specific finding** — what the review identified
   - **Acceptance criteria** — what "done" looks like
   - **Dependencies** — prerequisite tasks or cross-repo considerations
   - **Suggested approach** — implementation strategy, key files to modify, testing strategy
   - **Relevant standards & lessons** — summarize what was loaded in step 4

Ask: "Approve this work plan to begin implementation?"

6. Execute the implementation following the `/work-on-task` pattern. Implement the changes described in the approved work plan: modify source files, add or update tests, update docstrings and inline documentation. Use the correct conda environment for the target repo (core→`conda run -n core`, ssi→`conda run -n ssi`, ml→`conda run -n ml`, ui→`pnpm`, infra→`terraform`). Run tests after implementation to verify correctness.

// turbo

7. Run pre-commit validation on all changed files. For Python repos, run pre-commit twice (first pass auto-fixes, `git add -u`, second pass must exit clean). For UI, run `pnpm format` → `git add` → `pnpm lint` → `pnpm build`. For Infra, run `terraform fmt -check -recursive`. Fix any issues that arise and re-run until clean.

// turbo

8. Check off the completed task in the hardening/review plan file. Change `[ ]` to `[x]` for the task that was just implemented. Do NOT modify any other tasks or content in the plan — plans are append-only except for checkbox updates.

// turbo

9. Update `planning/change_log.md` in the target repo (or in `antigravity/` if the target repo has no change log). Append a new entry with today's date, the task ID, a one-line summary of what was done, and the list of modified files. Do not overwrite existing entries.

// turbo

10. Report a completion summary in structured format:
   - **Modified files** — list with one-line description of each change
   - **Test results** — pass/fail counts, any skipped tests
   - **Pre-commit status** — clean or issues encountered
   - **Updated plan progress** — new Done/Remaining counts
   - **Next unchecked task** — preview of what comes next in the hardening plan (or "Plan complete!" if all done)

// turbo
