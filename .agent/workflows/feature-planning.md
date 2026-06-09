---
description: Full feature planning pipeline — scope, repo analysis, architecture check, task breakdown, risk assessment
---

0. **Recommend project slice.** Before any planning work, identify which project slice provides the minimal context needed for this feature. Present the recommendation:

   | Slice | Repos | When to use |
   |-------|-------|------------|
   | `i4g-frontend` | antigravity, core, ui | UI-only or full-stack features |
   | `i4g-backend` | antigravity, core, ssi | API/service/DB changes |
   | `i4g-intelligence` | antigravity, core, ssi, ml | ML pipeline or model changes |
   | `i4g-infra` | antigravity, infra, core | Terraform/GCP changes |
   | `i4g-mobile` | antigravity, mobile, core | Mobile app features |
   | `i4g-docs` | antigravity, docs, planning | Documentation only |
   | `i4g-full` | all 9 repos | Cross-repo audits, architecture reviews |

   If the feature spans multiple slices (e.g., backend + frontend), recommend starting in the broader slice and delegating component-specific work to subagents scoped to narrower slices via `/delegate`.

   If the current session is already in a focused slice that matches, confirm it's appropriate and proceed. If the wrong slice is active, tell the user to start a new conversation in the recommended slice.
// turbo

1. **Clarify scope and intent.** Read the user's feature description. Identify the core problem, desired outcome, and any constraints mentioned. If the description is ambiguous, ask clarifying questions before proceeding. Summarize the scope in 3–5 bullet points covering: what the feature does, who it serves, and what systems it likely touches.
// turbo

2. **Scan all 9 repos for impact.** Search across all I4G repositories to identify affected code, shared types, API contracts, and cross-repo dependencies. Run the following scans:
   - `antigravity/` — shared knowledge, standards, skills
   - `core/` — backend API, models, services, Alembic migrations
   - `ssi/` — SSI service, DID/VC logic, credential flows
   - `ui/` — frontend components, API client, routes, state
   - `infra/` — Terraform modules, GCP resources, IAM
   - `ml/` — ML pipelines, model serving, training jobs
   - `mobile/` — mobile app screens, native modules
   - `planning/` — existing task files, change log, roadmap
   - `docs/` — user-facing docs, API references

   All repos live at `/Users/jerry/Work/project/i4g/<repo>/`. For each repo, search for files, types, endpoints, and modules related to the feature scope from Step 1. Produce a table:

   | Repo | Affected? | Key files / modules | Nature of change |
   |------|-----------|---------------------|------------------|

   Flag any cross-repo contract boundaries (shared Pydantic models, API routes consumed by UI, protobuf definitions, etc.).
// turbo

3. **Check architecture alignment.** Read `/Users/jerry/Work/project/i4g/antigravity/knowledge/architecture/architecture.md` and verify the proposed feature aligns with established patterns:
   - Service boundaries and layering (API → service → repository)
   - Data flow direction and event contracts
   - Auth/AuthZ model
   - Infrastructure topology

   Also read the relevant standards files from `/Users/jerry/Work/project/i4g/antigravity/knowledge/standards/` based on which repos are affected (e.g., `python.md` for core/ssi, `typescript.md` for ui, `terraform.md` for infra). Note any patterns the implementation must follow and any anti-patterns to avoid.

   Produce a short "Architecture Fit" summary: what aligns, what needs new patterns, and any architectural risks.
// turbo

4. **Identify risks and open questions.** Based on Steps 1–3, enumerate:
   - **Breaking changes** — API contract changes, DB schema changes, removed fields
   - **Migration needs** — Alembic migrations, data backfills, Terraform state changes
   - **Security considerations** — new auth scopes, secret management, PII handling
   - **Performance concerns** — new queries, indexing needs, caching implications
   - **Open questions** — anything that requires a design decision or external input

   Classify each risk as `HIGH`, `MEDIUM`, or `LOW` severity.
// turbo

5. **Draft the task plan.** Assemble a complete implementation plan as a markdown task checklist. Structure the plan as follows:

   ```
   # Feature: <feature name>
   **Scope:** <one-line summary>
   **Affected repos:** <list>
   **Risks:** <HIGH items only, one line each>

   ## Tasks

   ### Phase 1: <logical group, e.g., "Backend data model">
   - [ ] `core/path/to/file.py` — Description of change
   - [ ] `core/path/to/other.py` — Description of change

   ### Phase 2: <next logical group, e.g., "API endpoints">
   - [ ] `core/path/to/routes.py` — Description of change
   - [ ] `ui/path/to/client.ts` — Description of change

   ### Phase 3: ...
   (continue for all phases)

   ## Architecture Notes
   <key patterns to follow, references to standards>

   ## Open Questions
   <unresolved items from Step 4>
   ```

   Rules for the task list:
   - Every task must include an explicit file path (existing or `[NEW]`).
   - Group tasks into logical phases that can be executed and reviewed independently.
   - Order phases by dependency (data model → services → API → UI → infra → docs).
   - Include test tasks alongside implementation tasks, not as a separate phase.
   - Reference the correct environment per repo (conda env for Python repos, pnpm for UI, terraform for infra).

   Present the full draft plan to the user for review. Do NOT save it to disk yet.

   **Ask the user:** "Please review the plan above. Reply with approval, requested changes, or questions. Once approved, I'll save it and you can run `/work-on-task` to begin execution."

6. **Incorporate feedback.** If the user requests changes to the plan, revise the affected sections and re-present. Repeat until the user explicitly approves. Do not save anything to disk until approval is received.

7. **Save the approved plan.** Once the user approves, save the task plan to `/Users/jerry/Work/project/i4g/planning/tasks/<feature-slug>.md` using a kebab-case slug derived from the feature name. Also append a one-line entry to `/Users/jerry/Work/project/i4g/planning/change_log.md` noting the new plan.

   Confirm the saved file path and instruct the user:
   > "Plan saved. To begin implementation, run `/work-on-task` and point it at the saved task file."
// turbo
