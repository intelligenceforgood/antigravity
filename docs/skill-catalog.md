# Skill Catalog

Complete reference for every Antigravity skill available to the I4G team. Each entry includes the purpose, when to use it, what it expects as input, and what it produces.

---

## 📋 Planning Tier

### plan-work

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/plan-work` |
| **Shorthand** | "plan work for X" |
| **When** | Starting a new feature, refactoring initiative, or multi-step task |
| **Input** | Task description, PRD, user story, or feature request |
| **Output** | Implementation plan saved to `planning/tasks/` |
| **Next** | `work-on-task` for each step |
| **Recommended Model** | Opus 4.6 |

### prd

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/prd` |
| **Shorthand** | "create a PRD for X" |
| **When** | You have a feature idea but need it structured before planning |
| **Input** | Feature description, user feedback, or business requirement |
| **Output** | PRD saved to `planning/prds/` |
| **Next** | `arch` for technical design |
| **Recommended Model** | Opus 4.6 |

### arch

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/arch` |
| **Shorthand** | "design the architecture for X" |
| **When** | You have a PRD and need the technical approach before breaking into tasks |
| **Input** | PRD or feature description |
| **Output** | Architecture document saved to `planning/architecture/` |
| **Next** | `plan-work` for implementation planning |
| **Recommended Model** | Opus 4.6 |

---

## 💻 Execution Tier

### work-on-task

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/work-on-task` |
| **Shorthand** | "work on task step N" |
| **When** | You have a plan and need to implement one or more steps |
| **Input** | Reference to a plan file and the specific step(s) to execute |
| **Output** | Code changes, tests, docs updates, plan checkboxes updated |
| **Next** | Next step, or `code-review` when done |
| **Recommended Model** | Gemini 3.5 Flash (H/M) |

### tdd

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/tdd` |
| **Shorthand** | "TDD for X" |
| **When** | Implementing logic that benefits from test-first development |
| **Input** | Feature description or spec |
| **Output** | Tests written first, then implementation, then refactored |
| **Next** | `code-review` |
| **Recommended Model** | Gemini 3.5 Flash (H/M) / Gemini 3.1 Pro (Low) |

### fix-bug

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/fix-bug` |
| **Shorthand** | "fix the bug in X" |
| **When** | A bug has been reported or observed |
| **Input** | Stack trace, error description, reproduction steps |
| **Output** | Bug fix committed, regression test added |
| **Next** | `code-review` or `git-merge` |
| **Recommended Model** | Gemini 3.5 Flash (H/M) / Gemini 3.1 Pro (High) |

### clarify

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/clarify` |
| **Shorthand** | "clarify X" |
| **When** | The agent is blocked by ambiguity or contradiction in a plan |
| **Input** | Context from the current work session |
| **Output** | Structured clarification request; work paused |
| **Next** | User answers, then resume `work-on-task` |
| **Recommended Model** | Gemini 3.5 Flash / 3.1 Pro |

---

## 🔍 Review Tier

### code-review

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/code-review` |
| **Shorthand** | "review the code" or "code review" |
| **When** | Before merging; after completing implementation |
| **Input** | Staged or uncommitted changes (auto-detected via `git diff`) |
| **Output** | List of issues, improvements, or "Looks good to merge" |
| **Next** | `git-merge` if approved |
| **Recommended Model** | Sonnet 4.6 |

### lean-review

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/lean-review` |
| **Shorthand** | "lean review" or "quick review" |
| **When** | Sonnet quota is exhausted, or a quick automated pre-merge check is needed |
| **Input** | Staged or uncommitted changes (auto-detected via `git diff`) |
| **Output** | Pass/fail checklist: pre-commit, scope compliance, security scan, contract check |
| **Next** | `git-merge` if all checks pass |
| **Recommended Model** | Gemini 3.5 Flash |

### check-log

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/check-log` |
| **Shorthand** | "check the logs for X" |
| **When** | A Cloud Run job failed or you see errors in GCP logging |
| **Input** | GCP logging filter query |
| **Output** | Root cause analysis and proposed fix |
| **Next** | `fix-bug` if a code fix is needed |
| **Recommended Model** | Gemini 3.5 Flash (Low) / Gemini 3.1 Pro (High) |

### manual-verification

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/manual-verification` |
| **Shorthand** | "verify the deployment" |
| **When** | After deploying to dev or prod |
| **Input** | Environment to verify (dev or prod) |
| **Output** | Pass/fail for each verification check |
| **Next** | Fix issues or sign off |
| **Recommended Model** | Gemini 3.5 Flash (Low) |

---

## 🚀 Lifecycle Tier

### sprint-wrapup

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/sprint-wrapup` |
| **Shorthand** | "sprint wrapup" |
| **When** | End of a work session or sprint; before merging |
| **Input** | Current workspace state (auto-detected) |
| **Output** | Updated task plan, change log entry, risk assessment, lessons recorded |
| **Next** | `git-merge` |
| **Recommended Model** | Opus 4.6 / Gemini 3.1 Pro (High) |

### git-merge

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/git-merge` |
| **Shorthand** | "merge" or "git merge" |
| **When** | Changes are ready to go to `main` |
| **Input** | Changed repos (auto-detected via `git status`) |
| **Output** | Code reviewed, committed, and pushed to `main` |
| **Next** | `deploy-to-dev` if deploying |
| **Recommended Model** | Gemini 3.5 Flash (Low) |

### wrapup-and-merge

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/wrapup-and-merge` |
| **Shorthand** | "wrapup and merge" |
| **When** | You want the full end-of-sprint pipeline in one command |
| **Input** | Current workspace state |
| **Output** | Sprint wrapped up + code reviewed + committed + pushed |
| **Next** | `deploy-to-dev` |
| **Recommended Model** | Opus 4.6 |

### deploy-to-dev

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/deploy-to-dev` |
| **Shorthand** | "deploy to dev" |
| **When** | After merging, ready to deploy to `i4g-dev` |
| **Input** | Recently merged changes |
| **Output** | Pre-flight checks, Docker images built, deployment initiated |
| **Next** | `manual-verification` |
| **Recommended Model** | Gemini 3.5 Flash (Low) |

### record-lesson

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/record-lesson` |
| **Shorthand** | "record a lesson about X" |
| **When** | You learned something that future sessions should know |
| **Input** | The lesson (from conversation or user description) |
| **Output** | Entry added to `knowledge/operational/lessons-learned.md` |
| **Next** | Continue work |
| **Recommended Model** | Gemini 3.5 Flash (Low) |

### hardening-sprint

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/hardening-sprint` |
| **Shorthand** | "start hardening sprint" |
| **When** | Running through a platform review backlog |
| **Input** | Hardening plan in `planning/tasks/` |
| **Output** | Next task identified, executed, and checked off |
| **Next** | Repeat or `sprint-wrapup` |
| **Recommended Model** | Opus 4.6 |

---

## 💰 Token Optimization Tier

### prompt-preflight

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/prompt-preflight` |
| **Shorthand** | "preflight", "estimate this", "how much will this cost" |
| **When** | Before any large task, or when unsure which model to use |
| **Input** | User prompt or task description |
| **Output** | Brief advisory: complexity classification, model recommendation, decomposition suggestion |
| **Next** | Proceed with recommended model/skill, or decompose further |
| **Recommended Model** | Any (lightweight triage) |

### cost-estimate

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/cost-estimate` |
| **Shorthand** | "cost estimate", "how much quota", "estimate this plan" |
| **When** | After `plan-work` produces a plan, before starting implementation |
| **Input** | Task plan file from `planning/tasks/` |
| **Output** | Per-phase cost breakdown, total turns by model, session split recommendation |
| **Next** | Begin implementation per the session split |
| **Recommended Model** | Any (read-only analysis) |

### quota-mode

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/quota-mode` |
| **Shorthand** | "quota mode", "conserve tokens", "I'm low on quota" |
| **When** | Premium model quota is exhausted or running low |
| **Input** | None |
| **Output** | Conservation mode activated; list of available vs deferred skills |
| **Next** | Continue with execution-tier skills only |
| **Recommended Model** | Any (activates on any model) |

### codebase-digest

| Field | Value |
|---|---|
| **Skill Path** | `.agents/skills/codebase-digest` |
| **Shorthand** | "digest", "update digests", "refresh digests" |
| **When** | Weekly, after major changes, or when context maps are stale |
| **Input** | Active project slice |
| **Output** | Per-repo structural digests saved to `planning/digests/` |
| **Next** | Use digests instead of raw file reads in future sessions |
| **Recommended Model** | Gemini 3.5 Flash |

---

## Skill Chains (Common Sequences)

These are typical end-to-end skill chains for common development scenarios:

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

### Quota-Optimized Feature (Limited Budget)
```
prompt-preflight → plan-work (Opus) → cost-estimate → session-bridge → work-on-task (×N, Gemini) → lean-review (Gemini) → git-merge
```
