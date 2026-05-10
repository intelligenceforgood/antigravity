---
agent: antigravity
description: "Break a feature or task into actionable steps"
---

# Plan Work

**Role: Autonomous Agent (Antigravity).** Take a feature request, task description, or user story and break it into implementable steps. This routine produces a plan; it does not implement the code yet. **Always save the resulting plan document as a new markdown file in the `planning/tasks/` directory.** When the plan is ready, explicitly state the next step in the chat and ask the user if they want you to execute it. **CRITICAL: Do NOT output the markdown plan in your chat response. Since you are saving it directly, just summarize the plan briefly in chat.**

## Steps

1. **Clarify scope.** Read the task description (from `planning/`, a PR, or user input). If it's ambiguous, list assumptions and confirm with the user.

2. **Identify affected repos.** Determine which repos in the multi-root workspace need changes (`core/`, `ui/`, `ssi/`, `infra/`, etc.) and what kind of changes (API, UI, database, infrastructure).

3. **Check Knowledge Base.** Proactively review the architecture and language guidelines in `antigravity/knowledge/`. You are expected to adhere to the project patterns without the user needing to manually tag the files. Look for relevant patterns, especially:
   - Request routing (UI → API proxy → FastAPI)
   - Store/factory patterns
   - Worker/job patterns for background tasks

4. **Break into steps.** Draft the plan using the template structure below. Create a numbered task list:
   - Order by dependency (database first, then API, then UI)
   - Each step should be independently testable
   - Flag steps that require manual actions (migrations, deploys)
   - **CRITICAL**: Each step MUST explicitly list the exact files to be modified using absolute or workspace-relative paths to ensure subsequent execution is tightly scoped.

5. **Identify risks.** Note:
   - Breaking changes to existing APIs or schemas
   - New environment variables (need docs + tests)
   - Database migrations (need careful sequencing)

6. **Track with todos.** Create a todo list to track progress through the steps.

7. **Decide the next step.** Outline how the work should be incrementally executed:
   - Ask the user if they would like you to proceed with executing the first step autonomously.

## Template

**Objective:** {Brief description of the goal}

### 1. Milestones

Divide the work into logical phases (e.g., Database, Backend, Frontend).

### 2. Task Checklist

Create a markdown task list `- [ ]` for each step. Each task should be independently testable.

**CRITICAL:** Every task MUST explicitly mention the exact files it will modify.

- [ ] Step 1: ...
- [ ] Step 2: ...

---

**FINAL REMINDER FOR AGENT EXECUTION:**

1. You **MUST** use the `write_to_file` tool to save this document to the `planning/tasks/` directory.
2. You **MUST** use the exact `- [ ]` checkbox syntax for every task so progress can be tracked via `replace_file_content` during execution.
3. Do **NOT** output the plan text in your chat response. Just confirm the file was saved and summarize.
