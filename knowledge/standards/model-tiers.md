# Model Tier Definitions

> **Single source of truth** for the abstract model tier system used across all I4G skills, rules, and documentation.
> When the platform upgrades models, update the mapping table below — all other files reference tiers, not versions.

---

## Abstract Tiers

| Tier | Role | When to Use |
|:---|:---|:---|
| **Planning Tier** | Architecture, PRDs, task decomposition, cross-repo design, complex reasoning | `/prd`, `/arch`, `/plan-work`, `/hardening-sprint`, sprint planning |
| **Review Tier** | Code review, security audits, sprint wrapups, standards enforcement | `/code-review`, `/sprint-wrapup`, security audits |
| **Execution Tier (Light)** | Single-repo implementation, bug fixes, config changes, pattern-following tasks | `/work-on-task` (simple), `/fix-bug` (isolated), `/tdd`, `/check-log`, `/deploy-to-dev` |
| **Execution Tier (Heavy)** | Multi-file implementation, cross-repo bug traces, refactors touching shared types | `/work-on-task` (complex), `/fix-bug` (cross-repo), database migrations |
| **Utility Tier** | Model-agnostic tasks — session bridges, lessons, context maps, digests | `/session-bridge`, `/record-lesson`, `/context-map`, `/codebase-digest`, `/quota-mode` |

---

## Current Model Mapping

> [!IMPORTANT]
> Update this table when the platform adds or upgrades models. No other files need to change.

| Tier | Current Model(s) | Quota | Notes |
|:---|:---|:---|:---|
| **Planning Tier** | Opus 4.6 | Limited (~daily cap) | Highest reasoning quality. Use sparingly. |
| **Review Tier** | Sonnet 4.6 | Limited (higher cap) | Strong reasoning at moderate cost. |
| **Execution Tier (Light)** | Gemini 3.5 Flash (H/M/L) | Unlimited | Fast, cheap workhorse. One task per prompt. |
| **Execution Tier (Heavy)** | Gemini 3.1 Pro (H/L) | Unlimited | Better multi-step reasoning than Flash. Start with Flash, escalate if needed. |
| **Utility Tier** | Any | N/A | These skills run equally well on any model. |

---

## Complexity → Tier Mapping

| Complexity | Criteria | Recommended Tier |
|:---|:---|:---|
| 🟢 **SIMPLE** | Single repo, single file or small group, no API/schema changes, clear pattern | Execution Tier (Light) |
| 🟡 **MODERATE** | 1–2 repos, multiple files, follows existing patterns, may touch API routes | Execution Tier (Heavy) |
| 🔴 **COMPLEX** | 3+ repos, API contract changes, schema migrations, new architectural patterns | Planning Tier |

---

## Tier Usage Guidelines

- **Planning Tier**: Arrive with a clear question. Provide pre-loaded context (digests, context maps). Extract the plan, then switch to Execution Tier.
- **Review Tier**: Scope reviews to specific files/PRs. Use `/lean-review` on Execution Tier when Review Tier quota is exhausted.
- **Execution Tier**: Keep tasks narrow and file-isolated. Provide exact file paths, function names, and expected behavior. Start with Light; escalate to Heavy only when needed.
- **Utility Tier**: No quota concerns. Use freely on any model.

---

## Session Routing Pattern

```
Session 1 (Planning Tier)  → Planning, architecture, task decomposition
Session 2 (Execution Tier) → Implementation, testing, file generation
Session 3 (Review Tier)    → Review, security audit, standards check
```

Use `/session-bridge` between sessions to preserve context.

---

## Mapping Changelog

| Date | Change |
|:---|:---|
| 2026-07-14 | Initial creation. Mapping reflects Opus 4.6 / Sonnet 4.6 / Gemini 3.5 Flash / Gemini 3.1 Pro. |
