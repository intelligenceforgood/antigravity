# Quota Playbook

A practical guide to getting the most out of your AI quota across the I4G development workflow.

---

## Understanding Your Quota

Your AI Pro subscription gives you access to three tiers of models, each with different quota dynamics:

| Tier | Models | Quota | Role in I4G |
|:-----|:-------|:------|:------------|
| **Premium** | Opus 4.6 | Limited (~daily cap) | Planning, architecture, complex reasoning |
| **Standard** | Sonnet 4.6 | Limited (higher cap) | Code review, sprint wrapups, refactoring |
| **Unlimited** | Gemini 3.5 Flash, 3.1 Pro | Unlimited | Implementation, diagnostics, utilities |

Key things to know:

- **Quota resets on a rolling basis** — roughly every 5 days, not on a fixed calendar. Track your own patterns to predict availability.
- **Gemini models are always available.** Even when every premium model is exhausted, you can still implement, debug, and run utilities.
- **The I4G workflow is designed around these constraints.** Skills are tagged by model tier so you always know what's available to you.
- **One Opus turn doing planning can fuel 20+ Gemini turns of implementation.** Invest your premium quota in high-leverage planning, not line-by-line coding.

---

## Weekly Quota Budget Template

A suggested allocation for a typical development week. Adjust based on your reset schedule and project phase.

```
Monday:    Opus (2-3 turns) — Plan the week's work, create task plans
Tuesday:   Gemini (unlimited) — Implement from Monday's plans
Wednesday: Gemini (unlimited) — Continue implementation, TDD, bug fixes
Thursday:  Gemini (unlimited) — Finish implementation, write tests
Friday:    Sonnet (2-3 turns) — Code review, sprint wrapup, session bridge
Anytime:   Gemini (unlimited) — Diagnostics, log checking, context maps
```

> [!TIP]
> Front-load your Opus usage early in the week. A solid plan on Monday means you can implement autonomously for days without needing premium models.

---

## Opus-Free Day Patterns

You don't need Opus to be productive. Here's what you can accomplish with Gemini alone:

### Implementation Work
- **`/work-on-task`** — Implement tasks from existing plans (the plan is the context)
- **`/fix-bug`** — Diagnose and fix isolated bugs
- **`/tdd`** — Write tests and iterate on implementations

### Diagnostics & Operations
- **`/check-log`** — Diagnose production issues from log output
- **`/codebase-digest`** — Generate repo digests for future planning sessions
- **`/context-map`** — Map dependencies and understand code structure

### Review & Workflow (when Sonnet is also available)
- **`/lean-review`** — Quick automated pre-merge checks (works on Gemini!)
- **`/session-bridge`** — Save state for future sessions
- **`/git-merge`** — Run the merge workflow with automated checks

### What to Defer
- Architecture decisions → next Opus session
- New feature planning → next Opus session  
- Complex refactoring plans → next Opus session
- Full code reviews → next Sonnet session (or use `/lean-review` on Gemini)

---

## Emergency: Quota Exhausted Mid-Task

It happens. You're mid-flow and hit the quota wall. Here's the recovery playbook:

### Step 1: Save Your State
```
/session-bridge
```
This captures your current context, progress, and next steps so nothing is lost.

### Step 2: Switch to Gemini
Activate quota mode to signal that you're running on limited resources:
```
/quota-mode
```

### Step 3: Continue with Implementation Only
On Gemini, you can still:
- Finish implementing the current task if a plan exists
- Run tests and fix failures
- Run `/lean-review` instead of full code review
- Commit and push changes

### Step 4: Defer What You Can't Do
Queue these for your next premium session:
- Planning new tasks
- Architecture decisions
- Full code reviews (use `/lean-review` as a stopgap)
- Complex debugging that requires deep reasoning

### Step 5: Resume Seamlessly
When quota resets, start your next session with:
```
Read the session bridge at planning/session-bridge.md and resume where I left off.
```
The bridge document gives the new session full context without re-exploration.

---

## Prompt Engineering for Efficiency

The way you phrase prompts dramatically affects how much quota you consume. A well-structured prompt can save 3-5x the tokens of a vague one.

### Expensive vs. Efficient Prompts

**❌ Expensive** (triggers broad exploration):
> "Look at the codebase and figure out how to add export functionality"

This forces the model to scan dozens of files, reason about architecture, and make design decisions — all in one turn.

**✅ Efficient** (targeted, model-appropriate):
> "/plan-work: Add CSV export to the reports endpoint. Affected repos: core (API), ui (download button). See planning/prd_export.md for requirements."

This gives the model a clear scope, specific files, and a reference document — minimal exploration needed.

### More Tips

| Instead of… | Try… |
|:------------|:-----|
| "Fix the bug in the API" | "/fix-bug: 500 error on `POST /api/reports` when `status` is null. See `core/api/reports.py`." |
| "Review my changes" | "/lean-review" (on Gemini) or "/code-review" (on Sonnet) |
| "Help me understand this code" | "/codebase-digest for the `core/services/` directory" |
| "What should I work on?" | "Read `planning/tasks/active-sprint.md` and pick the next unblocked task." |

### General Principles

1. **Name exact files and functions** — don't make the model search for them.
2. **Specify the project slice** — "in `core/api/reports.py`" not "in the codebase."
3. **Use skill invocations** instead of free-form instructions — skills have built-in structure.
4. **Break large tasks into model-appropriate chunks** — plan on Opus, implement on Gemini.
5. **Always save a session bridge at natural pause points** — future sessions start faster.
6. **Include planning references** — "see `planning/prd_export.md`" gives instant context.

---

## AG2 Built-In Power Features

AG2 has built-in slash commands that can dramatically reduce your manual overhead. Use them to complement your custom skills:

### `/goal` — Autonomous Long-Running Tasks
Instead of manually chaining skills (plan → implement → review → merge), tell AG2 to complete the entire goal autonomously:

```
/goal: Implement all 🟢 SIMPLE tasks in planning/tasks/bulk-export.md, run tests, and prepare for review
```

AG2 will work continuously until the goal is achieved, chaining skills as needed. **Best for:** batches of simple implementation tasks on Gemini. **Avoid for:** tasks requiring architectural decisions (those need Opus planning first).

### `/teamwork-preview` — Multi-Agent Collaboration
For large features spanning 3+ repos, `/teamwork-preview` spawns a coordinated team of agents. This is a more powerful version of `/delegate` — AG2 handles the orchestration automatically.

```
/teamwork-preview: Implement the bulk export feature from planning/tasks/bulk-export.md
```

**Best for:** Large, well-planned features with independent work streams. **Requires:** An existing task plan with clear, file-isolated tasks.

### `/grill-me` — Interactive Design Interview
Before spending Opus tokens on `/prd` or `/arch`, use `/grill-me` to resolve design ambiguities through a structured Q&A:

```
/grill-me: I want to add bulk CSV export to the reports system
```

AG2 will ask targeted questions to nail down scope, edge cases, and trade-offs — so your subsequent planning session is focused and efficient.

### `/schedule` — Automated Maintenance
Set up recurring maintenance tasks:

```
/schedule: Run /codebase-digest every Monday at 9am
```

This keeps your repo digests fresh automatically, so every session starts with current structural context.

---

## Skills Quick Reference by Quota State

Use this table to quickly determine what's available based on your current quota:

| Quota State | Available Skills | Recommended Workflow |
|:------------|:-----------------|:---------------------|
| **Full quota** | All skills | Full workflow: plan → implement → review → merge |
| **Opus exhausted** | Execution + review + utility | Work from existing plans, review with Sonnet |
| **Opus + Sonnet exhausted** | Execution + utility only | `/quota-mode` → implement + `/lean-review` |
| **All premium exhausted** | Gemini only | Implementation from plans, `/lean-review`, defer everything else |

### Skill Tier Reference

| Tier | Skills |
|:-----|:-------|
| **Opus only** | `/plan-work`, `/architecture-review`, `/design-system` |
| **Sonnet** | `/code-review`, `/refactor`, `/session-bridge` |
| **Gemini (always available)** | `/work-on-task`, `/fix-bug`, `/tdd`, `/lean-review`, `/check-log`, `/codebase-digest`, `/context-map`, `/git-merge`, `/quota-mode` |

> [!NOTE]
> This table reflects the *recommended* model tier for each skill. In a pinch, Gemini can attempt any skill — the quality of planning and review tasks will just be lower. Implementation and utility tasks run equally well on Gemini.
