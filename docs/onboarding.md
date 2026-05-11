# Antigravity Developer Onboarding

Welcome to the **Antigravity Workflow Framework** for the I4G team. This guide walks you through setup, core concepts, and your first workflow.

## Prerequisites

- **Antigravity** installed and authenticated.
- Access to the I4G Git repositories (`core`, `ui`, `ssi`, `infra`, `ml`, `planning`, `docs`, `mobile`).
- The `antigravity` repository cloned alongside the other I4G repos under the same parent directory.

## Step 1: Workspace Configuration

Antigravity operates on absolute file paths and natively supports multi-root workspaces. Unlike the previous GCA setup, **you do not need**:
- A single unified workspace root
- Symlinks to `.gemini/` directories
- Any special VSCode workspace configuration

Simply ensure all your I4G repos (including `antigravity`) are under the same parent directory (e.g., `/path/to/i4g/`), and Antigravity will automatically detect and navigate all of them.

## Step 2: Configure Your User Rules

For consistent agent behavior across all sessions, add the recommended rules to your Antigravity settings. See [docs/user-rules-template.md](user-rules-template.md) for the full template.

Key rules to add:
- **Workflow awareness** — the agent will check for relevant workflows before starting work
- **Knowledge loading** — the agent will proactively read architectural standards before coding
- **Output discipline** — the agent won't dump large code blocks in chat
- **Planning discipline** — the agent will track task progress in plan files

These rules persist across ALL conversations — you only set them once.

## Step 3: Understand the Framework

### Knowledge (Automatic Context)

The `antigravity/knowledge/` directory contains architectural standards, coding conventions, and operational lessons. When Antigravity executes a workflow, it proactively reads the relevant knowledge files. **You never need to manually load these.**

```
knowledge/
├── architecture/    # Platform architecture, routing, auth, database schema
├── standards/       # Python, TypeScript, testing, security, CI/CD, Docker, etc.
└── operational/     # Lessons learned, merge discipline, workflow rules
```

### Workflows (Executable Procedures)

The `antigravity/workflows/` directory contains standardized procedures for every stage of development. Browse the [Workflow Index](../workflows/INDEX.md) to see all available workflows.

```
workflows/
├── planning/        # PRD → Architecture → Implementation Plan
├── execution/       # Work on Task, TDD, Fix Bug, Clarify
├── review/          # Code Review, Check Logs, Manual Verification
└── lifecycle/       # Sprint Wrapup, Merge, Deploy, Record Lessons
```

## Step 4: Invoke Your First Workflow

There are two ways to invoke a workflow:

### Method 1: `@[file]` Mention (Recommended)

Mention the workflow file in your prompt:

```
@[antigravity/workflows/planning/plan-work.md] Plan the new user search feature
described in planning/prd_user_search.md
```

Antigravity reads the workflow and follows its procedure autonomously.

### Method 2: Natural Language Shorthand

For common workflows, just describe what you want:

```
Plan work for the new search filtering feature
```

The agent recognizes the intent and follows the appropriate workflow. See the [Workflow Index](../workflows/INDEX.md) for all available shorthands.

## Step 5: Understanding Agent Autonomy

Antigravity is an **autonomous agent** — it reads files, writes code, and runs commands on your behalf. Here's what to expect:

### What the agent does automatically
- Reads source code, knowledge files, and plan documents
- Writes and edits code files
- Generates implementation plans and saves them to `planning/tasks/`
- Checks off completed tasks in plan files
- Records changes in `planning/change_log.md`

### What requires your approval
- **Shell commands** — the agent proposes commands (like `pytest`, `git push`, `pnpm build`) and waits for you to approve before executing
- **Destructive operations** — file deletions, force pushes, or database operations are flagged

### How to review its work
- Use `git diff` to see what the agent changed
- Check `planning/tasks/` for updated plan checklists
- Check `planning/change_log.md` for documented changes

## Step 6: Your First Workflow (Try It)

1. **Plan a feature:**
   ```
   @[antigravity/workflows/planning/plan-work.md] Plan a small improvement:
   add a "last updated" timestamp to the case detail page in the UI
   ```

2. **Execute step 1:**
   ```
   @[antigravity/workflows/execution/work-on-task.md] Work on task Step 1 from
   planning/tasks/<the-plan-file-created-above>.md
   ```

3. **Review the code:**
   ```
   @[antigravity/workflows/review/code-review.md] Review my staged changes
   ```

4. **Merge:**
   ```
   @[antigravity/workflows/lifecycle/git-merge.md] Merge the changes
   ```

## Session Management Tips

- **Stay in the same session** when working through a multi-step plan (Step 1, Step 2, Step 3...). The agent remembers context within a session.
- **Start a new session** when switching to a completely different feature or bug.
- **Don't worry about "rehydrating"** — Antigravity's Knowledge Items and conversation logs persist across sessions. Unlike GCA, there's no need to manually reload context at the start of each session.

---

Ready to dive deeper? Check the [Cookbook](cookbook.md) for real-world workflow recipes.
