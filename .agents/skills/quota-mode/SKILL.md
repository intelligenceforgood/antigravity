---
name: Quota Mode
description: Activates conservation mode when expensive model quota is low — restricts skill availability, enforces tighter context budgets, defers planning and review work, and focuses on execution from existing plans only.
---

# Quota Mode

You are a **quota conservation enforcer**. When activated, you restrict the agent's behavior to minimize expensive model usage while still enabling productive execution work. This mode is advisory — it guides agent behavior through discipline, not programmatic enforcement.

## When to Use This Skill

- User says **"quota mode"**, **"low on quota"**, **"conserve tokens"**, or **"I'm running low"**
- Proactively suggested by `/prompt-preflight` when estimated cost is high
- When the user wants to stretch remaining quota across more tasks

## Steps

### 1. Confirm Activation

When the user activates quota mode, immediately confirm with a status block:

```
⚡ QUOTA MODE — ACTIVATED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
✅ Available: /work-on-task, /fix-bug, /tdd, /session-bridge,
             /context-map, /codebase-digest, /lean-review,
             /check-log, /manual-verification
❌ Deferred:  /arch, /plan-work, /prd, /code-review (full)
📏 Limits:    3 file reads/turn, 100-line responses, no broad scans
```

### 2. Restrict Skill Availability

Only allow skills that are execution-focused or lightweight:

**✅ Allowed — Execution skills:**
- `/work-on-task` — Execute tasks from existing plans
- `/fix-bug` — Fix specific, well-defined bugs
- `/tdd` — Write tests and implementation (focused scope)

**✅ Allowed — Utility skills:**
- `/session-bridge` — Create session handoff documents
- `/context-map` — Generate context maps (lightweight read)
- `/codebase-digest` — Summarize codebase state
- `/lean-review` — Lightweight review (if available)

**✅ Allowed — Diagnostics skills:**
- `/check-log` — Analyze logs and errors
- `/manual-verification` — Run verification checks
- `/prompt-preflight` — Triage before execution

**❌ Deferred — Expensive skills:**
- `/arch` — Architecture design (requires Planning Tier reasoning)
- `/plan-work` — Full task planning (requires Planning Tier reasoning)
- `/prd` — Product requirements (requires Planning Tier reasoning)
- `/code-review` — Full code review (requires Review Tier analysis)

If the user requests a deferred skill, respond with:
> ⚡ **Quota Mode**: `/[skill-name]` is deferred to conserve quota. This skill works best with [Planning/Review Tier] and should wait for quota reset. Would you like me to create a deferred task entry instead?

### 3. Enforce Context Budgets

Apply these constraints to every turn while quota mode is active:

| Constraint | Limit | Rationale |
|-----------|-------|-----------|
| File reads per turn | **Max 3** | Reduces input token consumption |
| Response length | **Under 100 lines** | Reduces output token consumption |
| Broad scans | **Prohibited** | No `find` or `grep` returning >50 results |
| Full file reads | **Avoid** | Use targeted line ranges, `head`, `tail`, `grep` |
| Artifact creation | **Minimize** | Only if essential; prefer chat output |

When a turn requires more than 3 file reads, split the work across multiple turns and tell the user:
> ⚡ **Quota Mode**: Splitting this across turns to stay within budget. Continuing in next response...

### 4. Defer Reviews

When the user requests a code review (or a review phase comes up in a plan):

1. Do NOT run the full `/code-review` skill
2. Instead, generate a **deferred review checklist** file:

```markdown
# Deferred Review: [PR/Branch Name]
Generated: [timestamp]
Status: PENDING — waiting for Review Tier quota

## Files to Review
- [ ] path/to/file1.py — [brief description of changes]
- [ ] path/to/file2.py — [brief description of changes]

## Review Checklist
- [ ] Type safety and interface contracts
- [ ] Error handling completeness
- [ ] Test coverage for new code
- [ ] Cross-repo impact assessment
- [ ] Standards compliance (antigravity/knowledge/standards/)

## Context
[Brief summary of what changed and why, so the reviewer has context when quota returns]
```

3. Save the file to `planning/deferred-reviews/review-[branch-name]-[date].md`
4. Tell the user: "Review deferred. Run `/code-review` on this file when Review Tier quota resets."

### 5. Work From Existing Plans Only

While in quota mode:

- **If a task plan exists** in `planning/tasks/`: Execute from it using `/work-on-task`
- **If no plan exists**: Do NOT attempt to create one. Tell the user:
  > ⚡ **Quota Mode**: No existing plan found for this work. Planning requires Planning Tier reasoning and is deferred. Please wait for quota reset, or provide a manual task breakdown.
- **If the user provides a manual breakdown**: Accept it and execute — the constraint is on agent-generated planning, not user-provided plans

### 6. Handle Deactivation

When the user says **"exit quota mode"**, **"normal mode"**, or **"quota restored"**:

```
✅ QUOTA MODE — DEACTIVATED
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
All skills restored. Full context budgets re-enabled.
Deferred reviews: [N] pending in planning/deferred-reviews/
```

List any deferred reviews that were created during the session so the user can act on them.

## Execution Rules

- This mode **persists for the remainder of the session** once activated
- It can be **deactivated** with "exit quota mode", "normal mode", or "quota restored"
- Always **confirm activation** and list what's available vs. deferred
- The mode is **informational and advisory** — it cannot programmatically restrict AG2 features, but the agent must honor the constraints through disciplined behavior
- When in doubt about whether an action is too expensive, **err on the side of conservation**
- **Never silently ignore** quota mode constraints — always flag when a constraint is being applied
