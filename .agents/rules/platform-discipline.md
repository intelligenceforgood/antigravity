---
description: Cross-repo platform discipline for the I4G workspace
activation: always_on
---

# Platform Discipline

> Apply these rules whenever working in any I4G repository.

## Cross-Repo Standards Loading

All repos in the I4G workspace share the centralized standards in `antigravity/knowledge/`. Each standards file has a `repos:` frontmatter field listing which repos it applies to. **Only load standards files whose repos overlap with the active project slice.** Skip files for repos not in the current workspace.

When working in a focused slice (e.g., `i4g-frontend`), this means:
- ✅ Load `typescript.md` (repos: ui, mobile) — `ui` is in the slice
- ✅ Load `python.md` (repos: core, ssi, ml) — `core` is in the slice
- ❌ Skip `terraform.md` (repos: infra) — `infra` is not in the slice
- ✅ Always load `security.md` and `testing.md` (repos: all)

## Environment Routing

Each repo has its own Conda environment. Use the correct prefix:

| Repo | Conda Env | Command Prefix |
|------|-----------|----------------|
| `core` | `core` | `conda run -n core` |
| `ssi` | `ssi` | `conda run -n ssi` |
| `ml` | `ml` | `conda run -n ml` |
| `ui` | N/A | `pnpm` (run from `ui/` directory) |
| `infra` | N/A | `terraform` (with `gcloud` auth) |

## Pre-Commit Discipline

- **Python repos** (`core`, `ssi`): Two-pass pre-commit. Run once (auto-fixes), `git add -u`, run again (must exit clean).
- **UI repo**: Always run `pnpm format` before staging. Then `pnpm lint` and `pnpm build` before merging.
- **Infra repo**: `terraform fmt -check -recursive` must pass before merge.

## Safety Guardrails

- **Infra**: Always target `i4g-dev` before `i4g-prod`. Never apply to prod without explicit user confirmation.
- **Secrets**: Never hard-code API keys, passwords, or credentials. Use Secret Manager or `.env.local`.
- **Database**: Never run destructive migrations without user confirmation. Always create Alembic migrations; never modify the database schema directly.

## Tracking & Documentation

- When work is done from a `planning/tasks/` plan, check off tasks immediately.
- Update `planning/change_log.md` for any significant changes (append, don't overwrite).
- Record lessons learned via the `/record-lesson` skill when encountering pitfalls or discovering patterns.
