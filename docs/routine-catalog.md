# Routine Catalog

Complete reference for every Antigravity workflow available to the I4G team. Each entry includes the workflow purpose, when to use it, what it expects as input, and what it produces.

> For a compact table view, see the [Workflow Index](../workflows/INDEX.md).

---

## 📋 Planning Tier

### plan-work

| Field | Value |
|---|---|
| **File** | `workflows/planning/plan-work.md` |
| **Trigger** | `@[antigravity/workflows/planning/plan-work.md] <description>` |
| **Shorthand** | "plan work for X" |
| **When** | Starting a new feature, refactoring initiative, or multi-step task |
| **Input** | Task description, PRD, user story, or feature request |
| **Output** | Implementation plan saved to `planning/tasks/` |
| **Next** | `work-on-task` for each step |

### prd

| Field | Value |
|---|---|
| **File** | `workflows/planning/prd.md` |
| **Trigger** | `@[antigravity/workflows/planning/prd.md] <description>` |
| **Shorthand** | "create a PRD for X" |
| **When** | You have a feature idea but need it structured before planning |
| **Input** | Feature description, user feedback, or business requirement |
| **Output** | PRD saved to `planning/prds/` |
| **Next** | `arch` for technical design |

### arch

| Field | Value |
|---|---|
| **File** | `workflows/planning/arch.md` |
| **Trigger** | `@[antigravity/workflows/planning/arch.md] <description>` |
| **Shorthand** | "design the architecture for X" |
| **When** | You have a PRD and need the technical approach before breaking into tasks |
| **Input** | PRD or feature description |
| **Output** | Architecture document saved to `planning/architecture/` |
| **Next** | `plan-work` for implementation planning |

---

## 💻 Execution Tier

### work-on-task

| Field | Value |
|---|---|
| **File** | `workflows/execution/work-on-task.md` |
| **Trigger** | `@[antigravity/workflows/execution/work-on-task.md] <task reference>` |
| **Shorthand** | "work on task step N" |
| **When** | You have a plan and need to implement one or more steps |
| **Input** | Reference to a plan file and the specific step(s) to execute |
| **Output** | Code changes, tests, docs updates, plan checkboxes updated |
| **Next** | Next step, or `code-review` when done |

### tdd

| Field | Value |
|---|---|
| **File** | `workflows/execution/tdd.md` |
| **Trigger** | `@[antigravity/workflows/execution/tdd.md] <description>` |
| **Shorthand** | "TDD for X" |
| **When** | Implementing logic that benefits from test-first development |
| **Input** | Feature description or spec |
| **Output** | Tests written first, then implementation, then refactored |
| **Next** | `code-review` |

### fix-bug

| Field | Value |
|---|---|
| **File** | `workflows/execution/fix-bug.md` |
| **Trigger** | `@[antigravity/workflows/execution/fix-bug.md] <bug description>` |
| **Shorthand** | "fix the bug in X" |
| **When** | A bug has been reported or observed |
| **Input** | Stack trace, error description, reproduction steps |
| **Output** | Bug fix committed, regression test added |
| **Next** | `code-review` or `git-merge` |

### clarify

| Field | Value |
|---|---|
| **File** | `workflows/execution/clarify.md` |
| **Trigger** | `@[antigravity/workflows/execution/clarify.md]` |
| **Shorthand** | "clarify X" |
| **When** | The agent is blocked by ambiguity or contradiction in a plan |
| **Input** | Context from the current work session |
| **Output** | Structured clarification request; work paused |
| **Next** | User answers, then resume `work-on-task` |

---

## 🔍 Review Tier

### code-review

| Field | Value |
|---|---|
| **File** | `workflows/review/code-review.md` |
| **Trigger** | `@[antigravity/workflows/review/code-review.md]` |
| **Shorthand** | "review the code" or "code review" |
| **When** | Before merging; after completing implementation |
| **Input** | Staged or uncommitted changes (auto-detected via `git diff`) |
| **Output** | List of issues, improvements, or "Looks good to merge" |
| **Next** | `git-merge` if approved |

### check-log

| Field | Value |
|---|---|
| **File** | `workflows/review/check-log.md` |
| **Trigger** | `@[antigravity/workflows/review/check-log.md] <log filter>` |
| **Shorthand** | "check the logs for X" |
| **When** | A Cloud Run job failed or you see errors in GCP logging |
| **Input** | GCP logging filter query |
| **Output** | Root cause analysis and proposed fix |
| **Next** | `fix-bug` if a code fix is needed |

### manual-verification

| Field | Value |
|---|---|
| **File** | `workflows/review/manual-verification.md` |
| **Trigger** | `@[antigravity/workflows/review/manual-verification.md]` |
| **Shorthand** | "verify the deployment" |
| **When** | After deploying to dev or prod |
| **Input** | Environment to verify (dev or prod) |
| **Output** | Pass/fail for each verification check |
| **Next** | Fix issues or sign off |

---

## 🚀 Lifecycle Tier

### sprint-wrapup

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/sprint-wrapup.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/sprint-wrapup.md]` |
| **Shorthand** | "sprint wrapup" |
| **When** | End of a work session or sprint; before merging |
| **Input** | Current workspace state (auto-detected) |
| **Output** | Updated task plan, change log entry, risk assessment, lessons recorded |
| **Next** | `git-merge` |

### git-merge

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/git-merge.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/git-merge.md]` |
| **Shorthand** | "merge" or "git merge" |
| **When** | Changes are ready to go to `main` |
| **Input** | Changed repos (auto-detected via `git status`) |
| **Output** | Code reviewed, committed, and pushed to `main` |
| **Next** | `deploy-to-dev` if deploying |

### wrapup-and-merge

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/wrapup-and-merge.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/wrapup-and-merge.md]` |
| **Shorthand** | "wrapup and merge" |
| **When** | You want the full end-of-sprint pipeline in one command |
| **Input** | Current workspace state |
| **Output** | Sprint wrapped up + code reviewed + committed + pushed |
| **Next** | `deploy-to-dev` |

### deploy-to-dev

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/deploy-to-dev.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/deploy-to-dev.md]` |
| **Shorthand** | "deploy to dev" |
| **When** | After merging, ready to deploy to `i4g-dev` |
| **Input** | Recently merged changes |
| **Output** | Pre-flight checks, Docker images built, deployment initiated |
| **Next** | `manual-verification` |

### record-lesson

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/record-lesson.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/record-lesson.md] <lesson>` |
| **Shorthand** | "record a lesson about X" |
| **When** | You learned something that future sessions should know |
| **Input** | The lesson (from conversation or user description) |
| **Output** | Entry added to `knowledge/operational/lessons-learned.md` |
| **Next** | Continue work |

### hardening-sprint

| Field | Value |
|---|---|
| **File** | `workflows/lifecycle/hardening-sprint.md` |
| **Trigger** | `@[antigravity/workflows/lifecycle/hardening-sprint.md]` |
| **Shorthand** | "start hardening sprint" |
| **When** | Running through a platform review backlog |
| **Input** | Hardening plan in `planning/tasks/` |
| **Output** | Next task identified, executed, and checked off |
| **Next** | Repeat or `sprint-wrapup` |

---

## Workflow Chains (Common Sequences)

These are typical end-to-end workflow chains for common development scenarios:

### New Feature
```
prd → arch → plan-work → work-on-task (×N) → code-review → git-merge → deploy-to-dev → manual-verification
```

### Bug Fix
```
check-log → fix-bug → code-review → git-merge → deploy-to-dev
```

### End of Sprint
```
wrapup-and-merge (= sprint-wrapup + code-review + git-merge) → deploy-to-dev → manual-verification
```

### Hardening
```
hardening-sprint → work-on-task (×N) → sprint-wrapup → git-merge
```
