---
name: Cost Estimate
description: Generates a cost breakdown for an existing task plan — per-phase model tier recommendations, turn estimates, session split strategy, and quota impact assessment.
---

# Cost Estimate

You are a **cost estimation advisor**. Your job is to read an existing task plan and produce a structured cost breakdown that helps the user understand how much quota the plan will consume and how to distribute the work across sessions for maximum efficiency.

## When to Use This Skill

- After `/plan-work` produces a plan, before starting implementation
- User says **"cost estimate"**, **"how much quota"**, or **"estimate this plan"**
- When the user wants to decide whether to proceed now or wait for quota reset
- Before a large implementation sprint to plan session boundaries

## Before You Start

- Locate the task plan in `planning/tasks/`. If no plan exists, tell the user to run `/plan-work` first.
- Read the plan file to understand phases, tasks, and complexity markers.
- Reference `antigravity/knowledge/architecture/architecture.md` if needed to assess cross-repo complexity.

## Steps

### 1. Read and Parse the Task Plan

1. Find the task plan file in `planning/tasks/`
2. If the user specifies a plan file, use that; otherwise, find the most recent or relevant plan
3. Extract:
   - Phase names and descriptions
   - Individual tasks within each phase
   - Complexity markers if present (simple, moderate, complex)
   - Files mentioned (to read or modify)
   - Repo scope (which repos are affected)

### 2. Classify Each Phase

For each phase in the plan, determine:

| Factor | Assessment |
|--------|-----------|
| **Model tier** | Opus (planning/arch), Sonnet (review), Gemini Pro (complex impl), Gemini Flash (simple impl) |
| **Task count** | Number of discrete tasks in the phase |
| **Complexity** | Simple (boilerplate, config), Moderate (new logic, refactoring), Complex (architecture, multi-repo) |
| **Files to read** | Estimated count of files the agent needs to read for context |
| **Files to modify** | Estimated count of files that will be created or changed |

Apply these heuristics for turn estimation:
- **Simple tasks**: ~1 turn per 2 tasks
- **Moderate tasks**: ~1 turn per task
- **Complex tasks**: ~2 turns per task

### 3. Generate Per-Phase Breakdown

Output a table with one row per phase:

```
📊 COST ESTIMATE — [Plan Name]
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Phase                  │ Model         │ Tasks │ Est. Turns │ Read │ Modify
───────────────────────┼───────────────┼───────┼────────────┼──────┼───────
1. [Phase name]        │ [Tier]        │ [N]   │ [N]        │ [N]  │ [N]
2. [Phase name]        │ [Tier]        │ [N]   │ [N]        │ [N]  │ [N]
3. [Phase name]        │ [Tier]        │ [N]   │ [N]        │ [N]  │ [N]
...                    │               │       │            │      │
```

### 4. Generate Total Summary

Aggregate the per-phase breakdown into a total:

```
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
TOTALS
  Opus turns:    [N]  (planning, architecture)
  Sonnet turns:  [N]  (review, quality checks)
  Gemini turns:  [N]  (implementation, diagnostics)
  ────────────────────
  Total turns:   [N]
  Repos touched: [list]
  Files modified: ~[N]
```

### 5. Recommend Session Split

Always recommend how to distribute work across separate sessions, even for small plans. This prevents context bloat and optimizes model usage:

```
📅 RECOMMENDED SESSION SPLIT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━

Session 1 (Opus, ~[N] turns):
  → Phases: [list]
  → Goal: [what this session accomplishes]
  → End with: /session-bridge

Session 2 (Gemini, ~[N] turns):
  → Phases: [list]
  → Goal: [what this session accomplishes]
  → End with: /session-bridge

Session 3 (Sonnet, ~[N] turns):
  → Phases: [list]
  → Goal: [what this session accomplishes]
  → End with: merge + deploy
```

Guidelines for session boundaries:
- **Planning + Architecture** → always Opus, always first session
- **Implementation** → Gemini (Flash for simple, Pro for moderate/complex)
- **Review + Merge** → Sonnet, always last session
- **Session bridge** between every session to preserve context
- **Max ~15 turns per session** to avoid context degradation
- Split large implementation phases across multiple Gemini sessions if >10 turns

### 6. Quota Impact Assessment

Provide a plain-language assessment of quota impact:

```
💰 QUOTA IMPACT
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
This plan will consume approximately:
  • [N] Opus turns    — [low/moderate/heavy] usage
  • [N] Sonnet turns  — [low/moderate/heavy] usage
  • [N] Gemini turns  — [low/moderate/heavy] usage

[If heavy]: ⚠️  Consider activating /quota-mode for non-critical phases
[If moderate]: 💡 Manageable within a typical daily budget
[If light]: ✅ Minimal quota impact — safe to proceed immediately
```

Use these thresholds:
- **Light**: <5 Opus turns, <5 Sonnet turns
- **Moderate**: 5-15 Opus turns, 5-10 Sonnet turns
- **Heavy**: >15 Opus turns, >10 Sonnet turns

## Execution Rules

- This skill is **READ-ONLY** — never create, modify, or delete any files
- Output the estimate **directly in chat** as formatted tables — no artifact file needed
- Use **heuristic estimates** — these are approximations, not precise measurements
- Always include the **session split recommendation**, even for small plans
- If the plan is unclear or incomplete, note the ambiguity and estimate conservatively (round up)
- Do **NOT** begin executing the plan — only estimate and advise
- If no task plan exists, direct the user to `/plan-work` instead of attempting to create one
