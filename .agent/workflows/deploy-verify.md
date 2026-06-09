---
description: Pre-flight build, deploy to i4g-dev, and post-deployment verification with health checks and smoke tests
---

# Deploy & Verify

Pre-flight validation, deployment to `i4g-dev`, and post-deployment verification pipeline.

> **Safety:** Always target `i4g-dev` first. NEVER deploy to prod without explicit user confirmation.

## Context Boundary

Scan all repos to determine what needs deploying:

| Repo | Deploys | Image(s) |
|------|---------|----------|
| `core` | API + Workers | `core-svc`, `dossier-job`, `ingest-job`, `intake-job`, `report-job` |
| `ssi` | SSI service | SSI docker image |
| `ui` | Frontend | `i4g-console` |
| `infra` | Infrastructure | Terraform apply |
| `ml` | ML pipelines | Pipeline images |

## Steps

1. **Identify deployment scope.** For each repo, check `git log --oneline main..HEAD` and `git diff --stat` to determine what changed since the last deployment. Map changed files to Docker images that need rebuilding. Present a table: Repo | Changed Files | Images Affected.
// turbo

2. **Check for pending migrations.** Scan `core/` for new Alembic migration files (`alembic/versions/`). If found, list the migration(s) and note they must run before the new code deploys. Check `antigravity/knowledge/operational/lessons-learned.md` for known Alembic pitfalls.
// turbo

3. **Run local smoke test.** Verify the code works locally before deploying:
   ```bash
   cd /Users/jerry/Work/project/i4g/core
   conda run -n i4g I4G_PROJECT_ROOT=$PWD I4G_ENV=dev I4G_LLM__PROVIDER=mock i4g jobs ingest --help
   ```
   If UI changed, verify build: `cd /Users/jerry/Work/project/i4g/ui && pnpm build`
// turbo

4. **Pre-commit validation.** Run quality gates on all changed repos:
   - **Python**: `conda run -n <env> pre-commit run --all-files` (two-pass)
   - **UI**: `pnpm format && pnpm lint && pnpm build`
   - **Infra**: `terraform fmt -check -recursive && terraform validate`
// turbo

5. **Pre-flight summary.** Present a structured pre-flight report:
   - Images to build (with Dockerfile paths)
   - Migrations to run (if any)
   - Environment variables added/changed (check against `infra/environments/app/dev/terraform.tfvars`)
   - Local smoke test results
   - Pre-commit results
   
   **Wait for user approval before proceeding to build and deploy.**

6. **Build and push Docker images.** For each image identified in Step 1:
   ```bash
   cd <repo-root>
   scripts/build_image.sh <image-name> dev
   ```
   Report build status for each image.
// turbo

7. **Run database migrations.** If Alembic migrations were identified in Step 2:
   ```bash
   cd /Users/jerry/Work/project/i4g/core
   conda run -n i4g i4g db migrate dev
   ```
   ⚠️ If migration fails, STOP immediately. Do NOT proceed with deployment. Report the error and suggest diagnosis steps.
// turbo

8. **Post-deploy health checks.** After Cloud Run picks up the new images:
   - Check Cloud Run logs for startup errors (`/check-log` pattern)
   - Hit the health/readiness endpoint
   - Verify service is responding (not in crash loop)
// turbo

9. **API smoke tests and UI verification.** Test key functionality:
   - `GET /reviews/search` — search returns results
   - `GET /reviews/{id}` — single review lookup
   - If UI deployed: load the analyst console, verify search works end-to-end
   - If workers deployed: check recent job execution logs
// turbo

10. **Dev/prod parity check.** Verify production configs are ready for eventual prod deploy:
    - Compare `infra/environments/app/dev/terraform.tfvars` vs `infra/environments/app/prod/terraform.tfvars`
    - Flag any new env vars in dev that don't exist in prod
    - Flag any new Cloud Run jobs/services in dev not configured for prod
// turbo

11. **Verification summary.** Present a structured results report:

    | Check | Status | Details |
    |-------|--------|---------|
    | Health endpoint | ✅/❌ | Response code, latency |
    | API smoke tests | ✅/❌ | Endpoints tested, results |
    | UI verification | ✅/❌ | Pages loaded, features tested |
    | Worker health | ✅/❌ | Recent job status |
    | Dev/prod parity | ✅/❌ | Missing prod configs |

    Update `planning/change_log.md` with deployment record. **Wait for user sign-off.**
