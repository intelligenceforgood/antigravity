---
description: Proactive token advisory — complexity gating, model recommendations, session health, quota fallback
activation: always_on
---

# Quota Advisor

> Apply these advisories continuously. They fire as inline recommendations in chat output — not as blocking gates. The user retains full control over model selection.

## 1. Task Complexity Classification

Before executing any planning, architecture, or multi-repo skill, classify the task:

| Level | Criteria | Recommended Model |
|:---|:---|:---|
| 🟢 **SIMPLE** | Single repo, single file or small file group, no API/schema changes, clear pattern to follow | Gemini 3.5 Flash |
| 🟡 **MODERATE** | 1–2 repos, multiple files, follows existing patterns, may touch API routes but not contracts | Gemini 3.1 Pro |
| 🔴 **COMPLEX** | 3+ repos, API contract changes, schema migrations, new architectural patterns, shared type changes | Opus 4.6 |

**When to fire:** If the current model is Gemini and the classified complexity is 🔴 COMPLEX, emit:

> ⚠️ **Quota Advisory:** This task is classified as **COMPLEX** (reason: {specific reason}). Running this on Gemini risks architectural drift that will require Opus rework. Consider switching to Opus, or narrow the scope to a SIMPLE/MODERATE sub-task.

**When to fire (reverse):** If the current model is Opus and the classified complexity is 🟢 SIMPLE, emit:

> 💡 **Quota Advisory:** This task is classified as **SIMPLE**. You can save Opus quota by switching to Gemini Flash for this work.

## 2. Prompt Decomposition Advisor

When a user prompt contains multiple distinct work items (identifiable by: multiple verbs, "and also", "then", enumerated lists of unrelated changes, or requests spanning 3+ repos), emit:

> 💡 **Efficiency Advisory:** Your prompt contains ~{N} distinct work items. Breaking these into separate prompts allows:
> - Distributing across model tiers (plan on Opus, implement on Gemini)
> - Reducing per-session context accumulation
> - Enabling session bridging between items
>
> Suggested decomposition:
> 1. {item 1} → {recommended model}
> 2. {item 2} → {recommended model}
> ...

## 3. Session Fatigue Warning

Track approximate session depth by monitoring turn count and accumulated context. Fire warnings at these thresholds:

- **Turn 10**: Informational — "This session has accumulated moderate context."
- **Turn 15**: Advisory — suggest `/session-bridge` if tasks remain.
- **Turn 20+**: Strong advisory — "Session is deep. Performance and accuracy may degrade. Strongly recommend saving state with `/session-bridge` and continuing in a fresh session."

Format:

> 📊 **Session Health:** Turn ~{N}. {Advisory message}. Run `/session-bridge` to save state for a fresh session.

## 4. Quota Exhaustion Fallback

When the user reports that Opus or Sonnet quota is exhausted (or when a model switch fails), provide this guidance:

> 🔄 **Quota Fallback Guidance:**
>
> | Status | Action |
> |:---|:---|
> | ✅ Safe on Gemini | `/work-on-task`, `/fix-bug`, `/tdd`, `/check-log`, `/manual-verification`, `/context-map`, `/session-bridge`, `/codebase-digest` |
> | ⚠️ Reduced quality | `/code-review` (use `/lean-review` instead), `/sprint-wrapup` |
> | ❌ Defer until reset | `/arch`, `/plan-work`, `/prd`, cross-repo refactoring, API contract redesign |
>
> **Gemini-safe pattern:** Work from existing plans. Use `/work-on-task` to execute pre-planned tasks. Avoid ad-hoc architecture decisions.

## 5. Model-Aware Response Discipline

Adapt output behavior based on the active model:

- **On Opus**: Invest in thorough analysis but avoid reading files that Gemini will handle during implementation. Focus on design decisions, file mapping, and acceptance criteria — not code.
- **On Sonnet**: Focus on review quality. Avoid re-reading architecture docs already covered by the plan.
- **On Gemini**: Maximize implementation throughput. Keep responses under 50 lines. Use tools instead of explaining. Never attempt architecture-level reasoning — if blocked by a design question, invoke `/clarify` instead of guessing.

## Execution Rules

1. Advisories are **informational, not blocking**. Never refuse to execute a skill based on model mismatch — only advise.
2. Keep advisory messages to **3 lines maximum** in normal flow. Use the expanded format only on first occurrence per session.
3. Do NOT fire advisories for utility skills (`/session-bridge`, `/context-map`, `/record-lesson`) — these are model-agnostic.
4. The complexity classification does NOT replace the user's judgment. If the user explicitly acknowledges the advisory and proceeds, respect their decision.
