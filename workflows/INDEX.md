# Workflow Index

Complete catalog of all Antigravity workflows available to the I4G team. Organized by lifecycle tier.

> **How to invoke:** Mention the workflow file in your prompt with `@[file]` syntax, e.g.:
> `@[antigravity/workflows/planning/plan-work.md] Plan the new feature...`
>
> See [docs/workflow-dispatch.md](../docs/workflow-dispatch.md) for full invocation details.

---

## 📋 Planning

| Workflow | File | Purpose | Shorthand |
|---|---|---|---|
| **Plan Work** | `planning/plan-work.md` | Break a feature/task into implementable steps with milestones, checklists, and risk analysis | "plan work for X" |
| **PRD** | `planning/prd.md` | Generate a structured Product Requirements Document from a feature request | "create a PRD for X" |
| **Architecture** | `planning/arch.md` | Draft a technical architecture proposal ensuring compliance with I4G standards | "design the architecture for X" |

## 💻 Daily Execution

| Workflow | File | Purpose | Shorthand |
|---|---|---|---|
| **Work on Task** | `execution/work-on-task.md` | Implement a task from a plan with testing, documentation, and validation | "work on task step N" |
| **TDD** | `execution/tdd.md` | Implement via Test-Driven Development (Red → Green → Refactor) | "TDD for X" |
| **Fix Bug** | `execution/fix-bug.md` | Diagnose and fix a reported bug autonomously | "fix the bug in X" |
| **Clarify** | `execution/clarify.md` | Stop work and produce a structured clarification request when blocked | "clarify X" |

## 🔍 Review & Quality

| Workflow | File | Purpose | Shorthand |
|---|---|---|---|
| **Code Review** | `review/code-review.md` | Pre-merge review: cross-repo consistency, architecture, quality gates, tests | "review the code" |
| **Check Log** | `review/check-log.md` | Fetch and diagnose GCP Cloud Run logs | "check the logs for X" |
| **Manual Verification** | `review/manual-verification.md` | Post-deployment verification: health checks, API smoke tests, UI verification | "verify the deployment" |

## 🚀 Lifecycle

| Workflow | File | Purpose | Shorthand |
|---|---|---|---|
| **Sprint Wrapup** | `lifecycle/sprint-wrapup.md` | End-of-sprint documentation: drift check, changelog, risk assessment, lessons | "sprint wrapup" |
| **Git Merge** | `lifecycle/git-merge.md` | Full review + commit + push across all changed repos | "merge" / "git merge" |
| **Wrapup and Merge** | `lifecycle/wrapup-and-merge.md` | Combined sprint wrapup → code review → merge (the "big button") | "wrapup and merge" |
| **Deploy to Dev** | `lifecycle/deploy-to-dev.md` | Pre-flight checklist: smoke test, build, deploy to i4g-dev | "deploy to dev" |
| **Record Lesson** | `lifecycle/record-lesson.md` | Capture a lesson learned for future sessions | "record a lesson about X" |
| **Hardening Sprint** | `lifecycle/hardening-sprint.md` | Bootstrap a platform hardening session from the review/execution plan | "start hardening sprint" |
