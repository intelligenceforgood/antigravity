# How to Invoke Antigravity Workflows

This document explains how I4G developers trigger standardized workflows when working with Antigravity.

## The `@[file]` Dispatch Pattern (Primary)

To invoke a workflow, mention its file in your prompt using the `@[file]` syntax:

```
@[antigravity/workflows/planning/plan-work.md] Plan the new bulk export feature described in planning/prd_bulk_export.md
```

When you `@[file]` a workflow, Antigravity reads the file and treats its contents as **executable instructions**. The agent will then carry out the procedure described in the workflow autonomously.

### Why This Works

1. **Explicit** — You know exactly which workflow is being executed.
2. **Discoverable** — You can browse `antigravity/workflows/` in your file explorer to see what's available.
3. **Consistent** — Everyone on the team invokes the same workflow the same way.
4. **Version-controlled** — Workflow changes are tracked in git and reviewed via PR.

## Natural Language Shorthand (Secondary)

For frequently used workflows, you can also use plain English. The agent's persistent rules recognize common aliases:

| You Say | Workflow Invoked |
|---|---|
| "plan work for X" | `workflows/planning/plan-work.md` |
| "work on task step N" | `workflows/execution/work-on-task.md` |
| "fix the bug in X" | `workflows/execution/fix-bug.md` |
| "review the code" / "code review" | `workflows/review/code-review.md` |
| "merge" / "git merge" | `workflows/lifecycle/git-merge.md` |
| "wrapup and merge" | `workflows/lifecycle/wrapup-and-merge.md` |
| "check the logs for X" | `workflows/review/check-log.md` |
| "deploy to dev" | `workflows/lifecycle/deploy-to-dev.md` |

> **Note:** When in doubt, use the `@[file]` pattern — it's unambiguous. The natural language shortcuts are convenient but depend on the agent recognizing the intent.

## Chaining Workflows

Some workflows reference other workflows internally (e.g., `wrapup-and-merge.md` chains `sprint-wrapup.md` → `git-merge.md`). When the agent encounters such a reference, it reads the referenced workflow file and executes it as a sub-procedure.

You do not need to manually chain workflows. Just invoke the top-level one.

## Workflow Tiers

Workflows are organized into tiers by lifecycle stage:

```
workflows/
├── planning/       # PRD, architecture, implementation planning
├── execution/      # Task implementation, TDD, bug fixing, clarification
├── review/         # Code review, log analysis, manual verification
└── lifecycle/      # Sprint wrapup, merge, deploy, lessons learned
```

See the [Workflow Index](../workflows/INDEX.md) for the complete catalog.
