---
name: Session Bridge
description: Capture current session state as a structured handoff document for seamless cross-session continuity
---

# Session Bridge — Cross-Session State Transfer

**Role: Autonomous Agent.** Capture the current session's working state into a structured handoff document that enables a future session to resume from exactly where this one left off — without re-reading source files or re-discovering context.

## When to Use This Skill

- The session is approaching context window limits (agent mentions "long conversation" or performance degrades)
- The user says "save state", "bridge", "handoff", "prepare for next session", "wrap up for now"
- Before ending a long planning session with incomplete tasks
- When switching from Planning Tier to Execution Tier for implementation work

## Steps

### 1. Capture Task State

Read the active task file from `planning/tasks/` and extract:
- Which tasks are complete `[x]`, in progress `[/]`, and pending `[ ]`
- The current phase being worked on
- Any blockers or open questions

### 2. Capture Workspace State

Run across all repos in the active slice:
```bash
for repo in <repos>; do
  echo "=== $repo ==="
  cd /Users/jerry/Work/project/i4g/$repo
  git branch --show-current
  git status --porcelain
  git log --oneline -3
done
```

### 3. Capture Key Decisions

Review the conversation artifacts (implementation plan, task tracker) and extract:
- Architectural decisions made in this session
- Design trade-offs that were resolved
- User preferences or constraints expressed

### 4. Assemble Handoff Document

```markdown
# Session Bridge
Generated: <ISO timestamp>
Source session: <conversation ID>
Project slice: <active project>

## Active Task
- **Task file**: planning/tasks/<slug>.md
- **Current phase**: Phase <N> (<description>)
- **Completed**: tasks #<list>
- **In progress**: task #<N>
- **Blocked**: <none or description>

## Workspace State
| Repo | Branch | Uncommitted Changes | Recent Commits |
|------|--------|-------------------|----------------|

## Context Map Reference
- **Last generated**: planning/context-maps/<file>
- **Repo digests available**: planning/digests/<list files>

## Model Routing for Next Session
| Next Action | Recommended Model | Rationale |
|:---|:---|:---|
| <e.g., "Continue implementation Phase 2"> | Execution Tier (Light) | Narrow-scope coding from existing plan |
| <e.g., "Redesign pipeline architecture"> | Planning Tier | Cross-repo design decision |

## Quota Status Snapshot
- **Planning Tier**: <available / exhausted / unknown>
- **Review Tier**: <available / exhausted / unknown>
- **Execution Tier**: Always available
- **Recommendation**: <e.g., "Start next session on Execution Tier (Light) for implementation. Save Planning Tier for Phase 3 planning.">

## Pre-Loaded Context (read these first in next session)
1. This bridge document
2. planning/tasks/<slug>.md (task plan)
3. planning/digests/<relevant-repo>-digest.md
4. <specific source file only if mid-edit>

> **Do NOT re-read** antigravity/knowledge/architecture/architecture.md unless working on architecture. The digest files contain sufficient structural context.

## Key Decisions Made This Session
1. <decision + rationale>
2. <decision + rationale>

## Open Items for Next Session
1. <specific next step with file paths>
2. <specific next step>

## Recommended Next Action
<exact prompt the user should paste into the next session>
```

### 5. Save and Instruct

Save the bridge document to `planning/session-bridges/<date>-<slug>.md`.

Tell the user:
> "Session state saved. To resume in a new session:
> 1. Open a new conversation in the **<project slice>** project
> 2. Paste: `Resume from planning/session-bridges/<filename>. Continue with <next action>.`"

## Execution Rules

1. **Save to planning/session-bridges/**, not to conversation artifacts. Session bridges must survive across conversations.
2. **Include the exact resume prompt.** The user should be able to copy-paste one line to restart.
3. **Do NOT include code snippets.** The bridge is metadata — file paths, decisions, and state. The next session will read actual files as needed.
4. This skill is **read-only for source code** — it reads git state and conversation artifacts but never modifies source code.
