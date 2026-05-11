# Plan Work

**Role: Autonomous Agent.** Take a feature request, task description, or user story and break it into implementable steps. This routine produces a plan; it does not implement.

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

7. **Decide the next step.** Outline how the work should be incrementally executed — instruct the user to invoke the `work-on-task` workflow for each step.

## Template

**Objective:** {Brief description of the goal}

### 1. Milestones

Divide the work into logical phases (e.g., Database, Backend, Frontend).

### 2. Task Checklist

Create a markdown task list `- [ ]` for each step. Each task should be independently testable.

Every task MUST explicitly mention the exact files it will modify with full paths.

- [ ] Step 1: ...
- [ ] Step 2: ...

## Execution Rules

1. **Save the plan** using `write_to_file` to the `planning/tasks/` directory. Do NOT output the full plan text in chat — just confirm the file was saved and summarize.
2. Use the exact `- [ ]` checkbox syntax for every task so progress can be tracked.
3. After saving, tell the user the next step and how to invoke the `work-on-task` workflow.
