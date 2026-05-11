# Antigravity Cookbook: Real-World Workflows

This cookbook provides practical, copy-pasteable examples of how to use the Antigravity workflow framework to accomplish complex tasks within the I4G environment.

> **Golden Rule:** You are managing an autonomous engineer, not a chatbot. Give it context, point it to the workflow, and let it use its tools to do the heavy lifting.

---

## 🥘 Recipe 1: End-to-End Feature Development

**Scenario**: You need to implement a new API endpoint in `core` and a corresponding frontend component in `ui`.

**Steps:**

1. **Ideation** — Generate a PRD:
   ```
   @[antigravity/workflows/planning/prd.md] We need a new endpoint
   /api/v1/health/detailed that returns database connection status,
   and a React component to display this on the admin dashboard.
   ```
   The agent saves the PRD to `planning/prds/`.

2. **Architecture** — Design the technical approach:
   ```
   @[antigravity/workflows/planning/arch.md] Based on the PRD at
   planning/prds/detailed_health.md, propose the architecture.
   ```
   The agent reads the architecture knowledge, designs the solution, and saves it.

3. **Planning** — Break into implementable steps:
   ```
   @[antigravity/workflows/planning/plan-work.md] Create a step-by-step
   implementation plan for the detailed health feature based on the PRD
   and architecture docs in planning/.
   ```
   The agent creates a task checklist in `planning/tasks/`.

4. **Execution** — Implement step by step:
   ```
   @[antigravity/workflows/execution/work-on-task.md] Execute Step 1 from
   planning/tasks/detailed-health.md
   ```
   The agent reads the code, implements, writes tests, runs them, and checks off the step. Repeat for each step.

5. **Review** — Pre-merge code review:
   ```
   @[antigravity/workflows/review/code-review.md] Review the staged changes
   for the detailed health feature.
   ```
   The agent runs `git diff`, checks against standards, runs quality gates.

6. **Merge** — Commit and push:
   ```
   @[antigravity/workflows/lifecycle/git-merge.md] Merge the changes.
   ```
   The agent reviews, cleans, commits, and pushes all changed repos.

---

## 🐛 Recipe 2: Bug Hunting and Fixing

**Scenario**: A production bug is reported: "User profile updates are failing intermittently with a 500 error."

**Steps:**

1. **Diagnosis** — Analyze logs:
   ```
   @[antigravity/workflows/review/check-log.md] Check the logs for this error:
   resource.type = "cloud_run_job" resource.labels.job_name = "core-svc"
   severity>=ERROR timestamp >= "2026-05-10T00:00:00Z"
   ```
   The agent fetches logs via `gcloud`, identifies the root error, and traces it to source code.

2. **Fix** — Diagnose and fix:
   ```
   @[antigravity/workflows/execution/fix-bug.md] The update_profile function
   in core/src/i4g/api/users.py is failing with a database transaction timeout.
   Here is the stack trace: [PASTE]
   ```
   The agent finds the code, implements the fix, writes a regression test, and runs it.

3. **Record the lesson**:
   ```
   @[antigravity/workflows/lifecycle/record-lesson.md] Record the lesson about
   database transaction timeouts during profile updates.
   ```
   The agent adds it to `knowledge/operational/lessons-learned.md`.

---

## 🔄 Recipe 3: Session Continuation

**Scenario**: You're ending your day in the middle of a complex task and need to continue tomorrow (or hand off to a colleague).

**Steps:**

1. **Update the plan** — Make sure progress is tracked:
   ```
   Update the checkboxes in planning/tasks/auth-refactor.md to mark
   steps 1 and 2 as complete.
   ```

2. **Commit and push** your work-in-progress branch.

3. **Tomorrow (or your colleague)** — Start a new session:
   ```
   Read the remaining steps in planning/tasks/auth-refactor.md and
   summarize what needs to be done next.
   ```
   Antigravity retrieves context from the plan file. No "rehydration" prompt needed.

4. **Continue execution:**
   ```
   @[antigravity/workflows/execution/work-on-task.md] Work on the next
   unchecked step from planning/tasks/auth-refactor.md
   ```

> **Why this is simpler than GCA:** In GCA, you had to run `gca-prepare-new-session` to generate a copy-paste prompt, then `gca-rehydrate-session` to reload context. In Antigravity, the plan file IS the context. Knowledge Items persist automatically.

---

## 🛠️ Recipe 4: Deep Refactoring with Cross-Repo Impact

**Scenario**: You need to update a core utility function used in 50+ places across `core` and `ui`.

**Steps:**

1. **Analyze impact:**
   ```
   I need to change the signature of format_date() in core/src/i4g/utils/dates.py.
   Find all usages across the workspace and tell me the impact.
   ```
   The agent uses `grep_search` across all repos to find every usage.

2. **Plan the refactor:**
   ```
   @[antigravity/workflows/planning/plan-work.md] Plan the refactoring steps
   to safely update all usages of format_date(), prioritizing minimizing
   test breakages. The impact analysis is above.
   ```

3. **Execute in batches** — Use `work-on-task` for each step, with the agent running tests after each batch.

4. **Cross-repo merge:**
   ```
   @[antigravity/workflows/lifecycle/git-merge.md] Merge all the refactoring
   changes across core and ui.
   ```
   The agent ensures both repos are committed and pushed together.

---

## 🚀 Recipe 5: Platform Hardening Sprint

**Scenario**: You have a platform review with a backlog of security, performance, and reliability improvements.

**Steps:**

1. **Start the hardening session:**
   ```
   @[antigravity/workflows/lifecycle/hardening-sprint.md] Start a hardening sprint.
   ```
   The agent loads the review plan, identifies the next unchecked task, loads relevant context, and presents the work plan.

2. **Execute each task** — The agent follows the `work-on-task` pattern for each hardening item, checking them off as they complete.

3. **Sprint wrapup:**
   ```
   @[antigravity/workflows/lifecycle/sprint-wrapup.md] Sprint wrapup.
   ```
   The agent documents completed work, assesses risks, and determines merge readiness.

---

## 🔀 Recipe 6: Cross-Repo Merge Workflow

**Scenario**: You've made changes in `core`, `ui`, and `ssi` that need to ship together.

**Steps:**

1. **Wrapup and merge** — The single "big button":
   ```
   @[antigravity/workflows/lifecycle/wrapup-and-merge.md] Wrap up and merge
   all changes for the investigation pipeline feature.
   ```
   This chains three workflows automatically:
   - Sprint wrapup (document, assess risks, record lessons)
   - Code review (audit all changed repos)
   - Git merge (commit and push all repos simultaneously)

> **Key advantage over GCA:** The agent autonomously scans ALL repos for changes, generates diffs, runs quality gates, and pushes — without you needing to toggle Agent Mode or manually tag files.

---

## 📋 Recipe 7: GCP Log Diagnosis → Automated Fix

**Scenario**: A Cloud Run job failed overnight and you need to diagnose and fix it.

**Steps:**

1. **Copy the log filter** from the GCP Cloud Logging console.

2. **Diagnose:**
   ```
   @[antigravity/workflows/review/check-log.md] Check the logs for:
   resource.type = "cloud_run_job"
   resource.labels.job_name = "ingest-bootstrap"
   labels."run.googleapis.com/execution_name" = "ingest-bootstrap-xyz123"
   severity>=DEFAULT
   ```
   The agent:
   - Detects the GCP project (`i4g-dev`)
   - Fetches logs via `gcloud logging read`
   - Identifies the root error
   - Traces it to the source file

3. **Fix it** (if the agent deems it straightforward, it implements the fix automatically):
   ```
   @[antigravity/workflows/execution/fix-bug.md] Fix the error identified above.
   ```

4. **Deploy the fix:**
   ```
   @[antigravity/workflows/lifecycle/deploy-to-dev.md] Deploy the fix to dev.
   ```

---

## 💡 Tips for Effective Prompting

1. **Be specific about what you want.** "Fix the bug" is vague. "Fix the 500 error in the review search endpoint when filtering by date range" is actionable.

2. **Reference plan files.** Instead of re-describing a feature, point to the existing plan: "Work on Step 3 from `planning/tasks/bulk-export.md`."

3. **Let the agent read.** Don't paste entire files into chat. Say "Read `core/src/i4g/api/reviews.py` and explain the search logic" — the agent reads it directly.

4. **Trust the workflows.** The workflows encode hard-won operational knowledge. Use them instead of giving ad-hoc instructions for common tasks.

5. **Record lessons.** When you discover a new pitfall or pattern, use the `record-lesson` workflow. It gets captured in `knowledge/operational/lessons-learned.md` for all future sessions.
