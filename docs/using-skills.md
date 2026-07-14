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

| Tier | Recommended Tier | Skills Covered | Focus & Rationale |
|---|---|---|---|
| **Planning & Design** | **Planning Tier** | `/prd`, `/arch`, `/plan-work` | Architectural design, feature decomposition, and file mapping. Requires the absolute highest logical capacity. We do not use Execution Tier models for architecture. |
| **Code Review & Audits** | **Review Tier** | `/code-review`, `/sprint-wrapup` | High-value standards checking, security audits, and diff logic. |
| **Execution & Coding** | **Execution Tier (Light)** | `/work-on-task`, `/fix-bug`, `/tdd` | Writing code, running local tests, and making targeted edits. Highly fast and context-efficient. |
| **Complex Troubleshooting** | **Execution Tier (Heavy)** | `/fix-bug` (complex), `/sprint-wrapup` | Resolving multi-repo bugs, tracing detailed log stack traces, and checking complex integrations. |
| **Diagnostics & Ops** | **Execution Tier (Light)** | `/check-log`, `/manual-verification` | Processing raw log streams, running verification lists, and status checks. |

### Selecting the Right Execution Tier Weight
When executing implementation or diagnostic tasks, select the best tier weight:
* **Execution Tier (Light)**: Minor script changes, log dumping, running tests, standard coding, and standard unit tests. Minimizes token cost.
* **Execution Tier (Heavy)**: Multi-repo dependency resolution, trace log analysis, deep cross-repo bug fixes, and heavy refactoring.

When using subagent delegation (`/delegate`), the orchestrator automatically writes these model routing constraints into delegation briefs to ensure execution tasks run on the most cost-effective Execution Tier weight. See `knowledge/standards/model-tiers.md` for current model mappings.

---

## Quota-Optimized Workflow Playbook

Use this decision tree to determine the optimal workflow for any task, accounting for your current quota state:

```
Start → What kind of task is this?
│
├─ Planning / Architecture / PRD
│  └─ Is Planning Tier available?
│     ├─ YES → Use Planning Tier → /plan-work or /arch → /session-bridge → switch to Execution Tier
│     └─ NO  → DEFER. Do NOT plan on Execution Tier — it risks architectural drift.
│              Use Execution Tier for implementation of EXISTING plans only.
│
├─ Implementation (from existing plan)
│  └─ Check task annotation:
│     ├─ 🟢 SIMPLE → Execution Tier (Light) → /work-on-task
│     ├─ 🟡 MODERATE → Execution Tier (Heavy) → /work-on-task
│     └─ 🔴 COMPLEX → Wait for Planning Tier, or ask Planning Tier to decompose further
│
├─ Bug Fix
│  └─ Is it cross-repo?
│     ├─ YES → Execution Tier (Heavy) → /fix-bug
│     └─ NO  → Execution Tier (Light) → /fix-bug
│
├─ Code Review
│  └─ Is Review Tier available?
│     ├─ YES → Review Tier → /code-review
│     └─ NO  → Execution Tier (Light) → /lean-review (automated checks only)
│
└─ Diagnostics / Ops
   └─ Always Execution Tier (Light) → /check-log or /manual-verification
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

