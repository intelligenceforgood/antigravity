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

