---
description: Platform-wide code audit — standards compliance, security, dependency health, architecture drift across all 9 I4G repos
---

# Cross-Repo Audit

Platform-wide audit across all 9 I4G repositories. Checks standards compliance, security posture, dependency health, and architecture drift.

## Context Boundary

All 9 I4G repos are scanned:

| Repo | Path | Env | Quality Gate |
|------|------|-----|-------------|
| `core` | `/Users/jerry/Work/project/i4g/core/` | `conda run -n i4g` | pre-commit + pytest |
| `ssi` | `/Users/jerry/Work/project/i4g/ssi/` | `conda run -n i4g-ssi` | pre-commit + pytest |
| `ml` | `/Users/jerry/Work/project/i4g/ml/` | `conda run -n ml` | pre-commit + pytest |
| `ui` | `/Users/jerry/Work/project/i4g/ui/` | `pnpm` | format + lint + build |
| `infra` | `/Users/jerry/Work/project/i4g/infra/` | `terraform` | fmt -check + validate |
| `mobile` | `/Users/jerry/Work/project/i4g/mobile/` | `pnpm` | lint + typecheck |
| `antigravity` | `/Users/jerry/Work/project/i4g/antigravity/` | — | file integrity |
| `planning` | `/Users/jerry/Work/project/i4g/planning/` | — | markdown lint |
| `docs` | `/Users/jerry/Work/project/i4g/docs/` | — | link check |

## Steps

1. **Scan repo state.** For each of the 9 repos, run `git status --porcelain` and `git log --oneline -10` to capture dirty state and recent change velocity. Present a summary table of repo health.
// turbo

2. **Run quality gates.** Execute per-repo quality checks using the correct environment:
   - **Python** (`core`, `ssi`, `ml`): `conda run -n <env> pre-commit run --all-files`
   - **UI**: `cd ui && pnpm format --check && pnpm lint && pnpm build`
   - **Infra**: `cd infra && terraform fmt -check -recursive`
   - **Mobile**: `cd mobile && pnpm lint && pnpm typecheck`
   Record pass/fail for each repo.
// turbo

3. **Security scan.** Across all repos, check for:
   - Hard-coded secrets: grep for patterns `sk-`, `ghp_`, `AIza`, `AKIA`, `BEGIN RSA PRIVATE KEY`
   - Committed `.env` files that should be gitignored
   - Hard-coded local paths (`/Users/jerry/`)
   - Outdated dependencies: check `pyproject.toml` lock freshness, `pnpm outdated`, Terraform provider versions
// turbo

4. **Standards drift check.** For each repo, verify against `antigravity/knowledge/standards/`:
   - **Python repos**: Type hint coverage on public functions, Google-style docstrings, `get_settings()` usage (no hard-coded config), specific exception handling (no bare `except:`)
   - **UI repo**: Component naming conventions, proper use of server vs client components
   - **Infra repo**: Module structure, variable naming, tag consistency
// turbo

5. **Cross-repo dependency alignment.** Verify API contracts are consistent:
   - `core` ↔ `ui`: API route definitions match frontend fetch calls
   - `core` ↔ `ssi`: Shared types and service interfaces align
   - `core` ↔ `infra`: Environment variables referenced in code exist in Terraform configs
   - Settings manifest (`docs/config/settings_manifest.yaml`) matches actual env var usage
// turbo

6. **Architecture drift check.** Compare current code against `antigravity/knowledge/architecture/architecture.md`:
   - Request routing still follows UI → API proxy → FastAPI pattern
   - Store/factory patterns still used (not bypassed)
   - Worker/job patterns consistent
   - No unauthorized new service boundaries or direct DB access from UI
// turbo

7. **Compile findings report.** Assemble all findings into a structured report organized by:
   - **Severity**: 🔴 Critical → 🟠 High → 🟡 Medium → 🔵 Low
   - **Category**: Security, Architecture, Standards, Dependencies, Quality Gates
   Present as a table with: Severity | Category | Repo | Finding | Suggested Fix.
// turbo

8. **Review findings.** Present the consolidated findings report to the user. Wait for the user to triage: approve specific fixes, defer items, or dismiss false positives. Do NOT apply any fixes until the user has reviewed and approved specific items.

9. **Apply approved fixes.** For each user-approved finding, implement the fix following the `/work-on-task` pattern. Run relevant tests after each fix. Report modified files (no code in chat). Check lessons-learned for known pitfalls before each fix.
// turbo
