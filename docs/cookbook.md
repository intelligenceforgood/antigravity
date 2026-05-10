# Antigravity Cookbook

This cookbook provides concrete examples of how to format requests when interacting with the Antigravity autonomous agent. By using these prompt patterns, you ensure consistent code quality, adherence to our architecture, and fully leverage the agent's autonomous tool-calling capabilities.

## 1. Feature Planning

When starting a new feature, direct the agent to generate an implementation plan.

**User Prompt:**
> "Antigravity, please run the `plan-work.md` workflow to design the new 'User Bulk Export' feature defined in `planning/prd_bulk_export.md`. Break it down into testable steps."

**Expected Agent Action:**
- The agent reads `plan-work.md` from the `workflows/` directory.
- It scans the PRD.
- It proactively reads `antigravity/knowledge/architecture.md` to ensure the design aligns with the multi-root setup.
- It creates a task list in `planning/tasks/` and asks for your approval to begin Step 1.

## 2. Executing Tasks Incrementally

Once a plan exists, you can hand off the implementation step-by-step.

**User Prompt:**
> "Let's work on task Step 1 from `planning/tasks/user-bulk-export.md`. Use the `work-on-task.md` workflow."

**Expected Agent Action:**
- The agent reads the target files and implements the feature using its code editing tools.
- It writes unit tests.
- It runs `conda run -n i4g pytest` to verify the tests pass.
- It edits the `user-bulk-export.md` plan to check off `[x] Step 1`.
- It reports back with a summary of the modified files.

## 3. Autonomous Bug Fixing

When a bug occurs, give the agent the stack trace and point it to the relevant workflow.

**User Prompt:**
> "We are seeing a 500 error in the Next.js UI when attempting to load an investigation report. Please use the `fix-bug.md` workflow to diagnose and fix this. Here is the server log: [paste log]"

**Expected Agent Action:**
- The agent uses `grep_search` and `view_file` to locate the source of the 500 error across `core/` and `ui/`.
- It identifies the root cause (e.g., an incorrect proxy route).
- It fixes the code using `replace_file_content`.
- It runs relevant validation checks and reports back.

## 4. Pre-Merge Code Review

Before merging a branch, have Antigravity perform a standard code review.

**User Prompt:**
> "I have finished implementing the new search feature. Please execute the `code-review.md` workflow on the staged changes."

**Expected Agent Action:**
- The agent runs `git status` or `git diff` to identify all changed files.
- It checks the staged code against the styles in `antigravity/knowledge/`.
- It provides a summary of action items or offers to autonomously fix formatting and linting errors.

---
**Golden Rule:** You are managing an autonomous engineer, not a chatbot. Give it the context it needs, point it to the workflow, and let it use its tools to do the heavy lifting!
