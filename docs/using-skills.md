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
