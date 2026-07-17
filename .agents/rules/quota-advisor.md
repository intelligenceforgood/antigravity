---
description: Proactive token advisory — complexity gating, model recommendations, session health, quota fallback
activation: always_on
---

# Quota Advisor

> Apply these advisories continuously. They fire as inline recommendations in chat output — not as blocking gates. The user retains full control over model selection.
> See `knowledge/standards/model-tiers.md` for current tier-to-model mappings.

## 1. Task Complexity Classification

Before executing any planning, architecture, or multi-repo skill, classify the task:

| Level | Criteria | Recommended Tier |
|:---|:---|:---|
| 🟢 **SIMPLE** | Single repo, single file or small file group, no API/schema changes, clear pattern to follow | Execution Tier (Light) |
| 🟡 **MODERATE** | 1–2 repos, multiple files, follows existing patterns, may touch API routes but not contracts | Execution Tier (Heavy) |
| 🔴 **COMPLEX** | 3+ repos, API contract changes, schema migrations, new architectural patterns, shared type changes | Planning Tier |

**When to fire:** If the current model is an Execution Tier model and the classified complexity is 🔴 COMPLEX, emit:

> ⚠️ **Quota Advisory:** This task is classified as **COMPLEX** (reason: {specific reason}). Running this on the Execution Tier risks architectural drift that will require Planning Tier rework. Consider switching to a Planning Tier model, or narrow the scope to a SIMPLE/MODERATE sub-task.

**When to fire (reverse):** If the current model is a Planning Tier model and the classified complexity is 🟢 SIMPLE, emit:

> 💡 **Quota Advisory:** This task is classified as **SIMPLE**. You can save Planning Tier quota by switching to an Execution Tier model for this work.

## 1.5. Skill vs. Model Mismatch Advisory

Before executing any skill, check if the skill's recommended tier (defined in `docs/skill-catalog.md`) matches the current active model tier.

**When to fire:** If a Planning Tier skill (`/prd`, `/arch`, `/plan-work`, `/hardening-sprint`) or Review Tier skill (`/code-review`) is invoked while running on an Execution Tier model:

> [!WARNING]
> **Model Mismatch:** You are invoking a **Planning/Review Tier** skill (`{skill name}`) on an **Execution Tier** model (**{Active Model}**).
> Running planning or review tasks on execution models can lead to suboptimal plans, missing edge cases, or architectural drift.
> **Action:** Consider switching your model selection to a Planning/Review Tier model (e.g., **Gemini 3.1 Pro (High)**) before executing this skill.

## 2. Prompt Decomposition Advisor

When a user prompt contains multiple distinct work items (identifiable by: multiple verbs, "and also", "then", enumerated lists of unrelated changes, or requests spanning 3+ repos), emit:

> 💡 **Efficiency Advisory:** Your prompt contains ~{N} distinct work items. Breaking these into separate prompts allows:
> - Distributing across model tiers (plan on Planning Tier, implement on Execution Tier)
> - Reducing per-session context accumulation
> - Enabling session bridging between items
>
> Suggested decomposition:
> 1. {item 1} → {recommended tier}
> 2. {item 2} → {recommended tier}
> ...

## 3. Session Fatigue Warning

Track approximate session depth by monitoring turn count and accumulated context. Fire warnings at these thresholds:

- **Turn 10**: Informational — "This session has accumulated moderate context."
- **Turn 15**: Advisory — suggest `/session-bridge` if tasks remain.
- **Turn 20+**: Strong advisory — "Session is deep. Performance and accuracy may degrade. Strongly recommend saving state with `/session-bridge` and continuing in a fresh session."

Format:

> 📊 **Session Health:** Turn ~{N}. {Advisory message}. Run `/session-bridge` to save state for a fresh session.

## 4. Quota Exhaustion Fallback

When the user reports that Planning Tier or Review Tier quota is exhausted (or when a model switch fails), provide this guidance:

> 🔄 **Quota Fallback Guidance:**
>
> | Status | Action |
> |:---|:---|
> | ✅ Safe on Execution Tier | `/work-on-task`, `/fix-bug`, `/tdd`, `/check-log`, `/manual-verification`, `/context-map`, `/session-bridge`, `/codebase-digest` |
> | ⚠️ Reduced quality | `/code-review` (use `/lean-review` instead), `/sprint-wrapup` |
> | ❌ Defer until reset | `/arch`, `/plan-work`, `/prd`, cross-repo refactoring, API contract redesign |
>
> **Execution Tier-safe pattern:** Work from existing plans. Use `/work-on-task` to execute pre-planned tasks. Avoid ad-hoc architecture decisions.

## 5. Model-Aware Response Discipline

Adapt output behavior based on the active model tier:

- **On Planning Tier**: Invest in thorough analysis but avoid reading files that Execution Tier will handle during implementation. Focus on design decisions, file mapping, and acceptance criteria — not code.
- **On Review Tier**: Focus on review quality. Avoid re-reading architecture docs already covered by the plan.
- **On Execution Tier**: Maximize implementation throughput. Keep responses under 50 lines. Use tools instead of explaining. Never attempt architecture-level reasoning — if blocked by a design question, invoke `/clarify` instead of guessing.

## Execution Rules

1. Advisories are **informational, not blocking**. Never refuse to execute a skill based on model mismatch — only advise.
2. Keep advisory messages to **3 lines maximum** in normal flow. Use the expanded format only on first occurrence per session.
3. Do NOT fire advisories for Utility Tier skills (`/session-bridge`, `/context-map`, `/record-lesson`) — these are model-agnostic.
4. The complexity classification does NOT replace the user's judgment. If the user explicitly acknowledges the advisory and proceeds, respect their decision.
