---
name: Plan Work
description: Break a feature/task into implementable steps with milestones, checklists, and risk analysis
model: Opus 4.6
---

# Plan Work

**Role: Autonomous Agent.** Take a feature request, task description, or user story and break it into implementable steps. This routine produces a plan; it does not implement.

## When to Use This Skill

- Starting a new feature, refactoring initiative, or multi-step task
- The user says "plan", "break down", "create steps", or "implementation plan"
- A PRD or architecture doc exists and the next step is to create actionable tasks

## Before You Start

1. Proactively read `antigravity/knowledge/architecture/architecture.md` to understand the platform.
2. Read the relevant language standards from `antigravity/knowledge/standards/` based on which repos will be affected.

## Steps

1. **Clarify scope.** Read the task description (from `planning/`, a PR, or user input). If it's ambiguous, list assumptions and confirm with the user.

2. **Identify affected repos.** Determine which repos need changes (`core/`, `ui/`, `ssi/`, `infra/`, etc.) and what kind of changes (API, UI, database, infrastructure).

3. **Check architecture.** Review the architecture cheat sheet for relevant patterns, especially:
   - Request routing (UI → API proxy → FastAPI)
   - Store/factory patterns
   - Worker/job patterns for background tasks

4. **Break into steps.** Draft the plan using the template structure below. Create a numbered task list:
   - Order by dependency (database first, then API, then UI)
   - Each step should be independently testable
   - Flag steps that require manual actions (migrations, deploys)
   - Each step MUST explicitly list the exact files to be modified with full paths

5. **Identify risks.** Note:
   - Breaking changes to existing APIs or schemas
   - New environment variables (need docs + tests)
   - Database migrations (need careful sequencing)

6. **Track with todos.** Create a todo list to track progress through the steps.

7. **Decide the next step.** Outline how the work should be incrementally executed — instruct the user to invoke the `/work-on-task` skill for each step.

## Template

**Objective:** {Brief description of the goal}

### 1. Milestones

Divide the work into logical phases (e.g., Database, Backend, Frontend).

### 2. Task Checklist

Create a markdown task list `- [ ]` for each step. Each task should be independently testable.

Every task MUST explicitly mention the exact files it will modify with full paths.

**Gemini-safety annotations**: Tag each task with a complexity indicator so the user can instantly see which model is needed:
- 🟢 **SIMPLE** — Safe for Gemini Flash. Single repo, follows existing patterns.
- 🟡 **MODERATE** — Use Gemini Pro. Multiple files, may touch API routes.
- 🔴 **COMPLEX** — Requires Opus. Cross-repo contracts, schema changes, new patterns.

Example:
- [ ] 🟢 Step 1: Add `export_format` field to Report model (`core/src/i4g/models/report.py`) — SIMPLE
- [ ] 🟢 Step 2: Create export endpoint (`core/src/i4g/api/reports.py`) — follows existing CRUD pattern
- [ ] 🟡 Step 3: Add SSI export adapter (`ssi/src/ssi/adapters/export.py`) — touches cross-repo types
- [ ] 🔴 Step 4: Redesign report pipeline architecture — COMPLEX, requires Opus

## Execution Rules

1. **Save the plan** using `write_to_file` to the `planning/tasks/` directory. Do NOT output the full plan text in chat — just confirm the file was saved and summarize.
2. Use the exact `- [ ]` checkbox syntax for every task so progress can be tracked.
3. After saving, tell the user the next step and how to invoke `/work-on-task`.
4. **Model Routing**: This is a planning-tier skill. Run this skill strictly on **Opus 4.6** to ensure complete file coverage mapping. Do not use Gemini Pro.
5. **Cheaper Execution Gearing**: When breaking the task into steps, design them to be executed independently by low-cost models (Gemini 3.5 Flash / 3.1 Pro). Ensure each task defines a narrow, file-isolated boundary.
6. **Gemini-safety annotations**: Every task MUST have a 🟢/🟡/🔴 annotation. This enables the user to plan model allocation before starting implementation.
7. **Cost-aware ordering**: Order tasks so all 🟢 SIMPLE tasks can be batched into a single Gemini session, followed by 🟡 MODERATE tasks, with 🔴 COMPLEX tasks deferred to Opus sessions.
