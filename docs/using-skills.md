# How to Use Antigravity Skills

This document explains how I4G developers trigger standardized agentic capabilities when working with Antigravity.

## Native Skills

The I4G workspace is equipped with **Antigravity Skills**, which are native agentic capabilities.

The most efficient and native way to invoke a skill is by using **slash commands**. Simply type `/` in the chat, followed by the skill name:

```text
/plan-work for the new bulk export feature described in planning/prd_bulk_export.md
```

This immediately signals to the agent which specific skill protocol to load and execute.

### Natural Language (Secondary)

You can also invoke skills using natural language if you prefer, simply by referencing the intent:

```text
Please plan work for the new bulk export feature...
```

When you ask the agent, Antigravity automatically detects the relevant skill from its indexed catalog (located in `.agents/skills/`), reads its internal instructions, and executes the procedure autonomously.

### Why This Works

1. **Native Integration** — Skills are a first-class feature of Antigravity; the agent knows about them immediately.
2. **Discoverable** — Skills are displayed natively in the agent's context and can be browsed in `.agents/skills/`.
3. **Consistent** — The agent reliably follows the encapsulated instructions without needing exact file path syntax.
4. **Version-controlled** — Skill definitions are tracked in git and reviewed via PR.

## Skill Chaining

Some skills reference other skills internally (e.g., `wrapup-and-merge` chains `sprint-wrapup` → `git-merge`). When the agent encounters such a reference, it natively invokes the referenced skill and executes it as a sub-procedure.

You do not need to manually chain skills. Just invoke the top-level one.

### Autonomous Chaining with `/goal`

For longer chains of work, AG2's built-in `/goal` command can execute multiple skills autonomously until the goal is fully achieved. Instead of manually invoking each skill in a chain, describe the end state:

```text
/goal: Implement all 🟢 tasks in planning/tasks/bulk-export.md, run tests, do a lean review, and merge
```

AG2 will chain `/work-on-task` → `/lean-review` → `/git-merge` automatically. Use this on **Gemini** for implementation-heavy work to maximize throughput without manual intervention.

## Skill Categories

Skills are organized conceptually by lifecycle stage:

- **Planning**: PRD, architecture, implementation planning
- **Execution**: Task implementation, TDD, bug fixing, clarification
- **Review**: Code review, log analysis, manual verification
- **Lifecycle**: Sprint wrapup, merge, deploy, lessons learned

See the [Skill Catalog](skill-catalog.md) for the complete list of available skills.

---

## Model Routing & Token Economy

To optimize token utilization and API cost (especially for subscription tiers like AI Pro), follow this model-routing mapping in Antigravity 2.0:

| Tier | Recommended Model | Skills Covered | Focus & Rationale |
|---|---|---|---|
| **Planning & Design** | **Opus 4.6** | `/prd`, `/arch`, `/plan-work` | Architectural design, feature decomposition, and file mapping. Requires the absolute highest logical capacity. We do not use Gemini Pro for architecture. |
| **Code Review & Audits** | **Sonnet 4.6** | `/code-review`, `/sprint-wrapup` | High-value standards checking, security audits, and diff logic. |
| **Execution & Coding** | **Gemini 3.5 Flash (H/M)** | `/work-on-task`, `/fix-bug`, `/tdd` | Writing code, running local tests, and making targeted edits. Gemini 3.5 Flash is highly fast and context-efficient. |
| **Complex Troubleshooting** | **Gemini 3.1 Pro (H/L)** | `/fix-bug` (complex), `/sprint-wrapup` | Resolving multi-repo bugs, tracing detailed log stack traces, and checking complex integrations. |
| **Diagnostics & Ops** | **Gemini 3.5 Flash (Low)** | `/check-log`, `/manual-verification` | Processing raw log streams, running verification lists, and status checks. |

### Selecting the Right Gemini Model
When executing implementation or diagnostic tasks, use this checklist to select the best Gemini variant:
* **Gemini 3.5 Flash (Low)**: Minor script changes, log dumping, running tests, or single-line fixes. Minimizes token cost.
* **Gemini 3.5 Flash (Medium)**: Normal component editing, standard function writes, and standard unit tests.
* **Gemini 3.5 Flash (High)**: Creating large files/components, heavy refactoring of single-repo structures, and massive unit test files.
* **Gemini 3.1 Pro (Low)**: Minor API contract alignment, type definitions, and complex `/tdd` iterations.
* **Gemini 3.1 Pro (High)**: Multi-repo dependency resolution, trace log analysis, and deep cross-repo bug fixes.

When using subagent delegation (`/delegate`), the orchestrator (**Opus 4.6**) automatically writes these model routing constraints into delegation briefs to ensure execution tasks run on the most cost-effective Gemini Flash variant.

---

## Quota-Optimized Workflow Playbook

Use this decision tree to determine the optimal workflow for any task, accounting for your current quota state:

```
Start → What kind of task is this?
│
├─ Planning / Architecture / PRD
│  └─ Is Opus available?
│     ├─ YES → Use Opus → /plan-work or /arch → /session-bridge → switch to Gemini
│     └─ NO  → DEFER. Do NOT plan on Gemini — it risks architectural drift.
│              Use Gemini for implementation of EXISTING plans only.
│
├─ Implementation (from existing plan)
│  └─ Check task annotation:
│     ├─ 🟢 SIMPLE → Gemini Flash → /work-on-task
│     ├─ 🟡 MODERATE → Gemini Pro → /work-on-task
│     └─ 🔴 COMPLEX → Wait for Opus, or ask Opus to decompose further
│
├─ Bug Fix
│  └─ Is it cross-repo?
│     ├─ YES → Gemini Pro → /fix-bug
│     └─ NO  → Gemini Flash → /fix-bug
│
├─ Code Review
│  └─ Is Sonnet available?
│     ├─ YES → Sonnet → /code-review
│     └─ NO  → Gemini Flash → /lean-review (automated checks only)
│
└─ Diagnostics / Ops
   └─ Always Gemini Flash (Low) → /check-log or /manual-verification
```

### Session Planning Checklist

Before starting any work session:

1. **Check quota**: Which model tiers are available?
2. **Check task plan**: Do existing plans with 🟢/🟡 tasks exist?
3. **Pick the right model**: Match your available quota to the task tier
4. **Set a turn budget**: Aim for 10–15 turns maximum per session
5. **Plan your exit**: Know when you'll run `/session-bridge`

### Key Efficiency Skills

| Skill | Purpose | When to Use |
|:---|:---|:---|
| `/prompt-preflight` | Estimate cost before starting | Before any large task |
| `/quota-mode` | Activate conservation mode | When premium quota is low |
| `/cost-estimate` | Cost breakdown for a plan | After `/plan-work`, before implementation |
| `/codebase-digest` | Generate structural digests | Weekly, or after major changes |
| `/lean-review` | Quick automated review | When Sonnet is unavailable |
| `/session-bridge` | Save state for next session | At turn 15, or when switching models |

See [Quota Playbook](quota-playbook.md) for detailed guidance on managing your weekly quota budget.

