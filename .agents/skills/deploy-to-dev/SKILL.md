---
name: Deploy to Dev
description: Pre-flight checklist: smoke test, build, deploy to i4g-dev
---

# Deploy to Dev

**Role: Autonomous Agent.** Pre-flight checklist before deploying to the `i4g-dev` environment.

## When to Use This Skill

- Changes have been merged and need to be deployed to the dev environment
- The user says "deploy", "deploy to dev", "push to cloud", or "build and deploy"
- Following a successful `/git-merge` when cloud deployment is needed

## Before You Start

1. Read `antigravity/knowledge/standards/ci-cd.md` and `antigravity/knowledge/standards/docker.md`.

## Steps

1. **Pre-merge review first.** Ensure the pre-merge review has been completed. If not, read and execute the `/code-review` skill (`.agents/skills/code-review/SKILL.md`) first.

2. **Local smoke test.** Verify the code works locally:
   ```bash
   conda run -n core I4G_PROJECT_ROOT=$PWD I4G_ENV=dev I4G_LLM__PROVIDER=mock i4g jobs ingest --help
   ```

3. **Identify images to build.** Based on changed files, determine which Docker images need rebuilding:
   - Core API changes → `core-svc`
   - Worker/job changes → `dossier-job`, `ingest-job`, `intake-job`, `report-job`
   - UI changes → `cd ui/ && scripts/build_image.sh i4g-console dev`
   - SSI changes → check ssi docker configs

4. **Database migrations.** If Alembic migrations were added:
   ```bash
   i4g db migrate dev
   ```
   ⚠️ Run on dev first. Never migrate prod without a successful dev migration.

5. **Build and push images.** From the appropriate repo root:
   ```bash
   scripts/build_image.sh <image-name> dev
   ```

6. **Post-deploy verification.** After Cloud Run picks up the new image:
   - Check Cloud Run logs for startup errors
   - Hit the health endpoint
   - Consider running the `/manual-verification` skill

7. **Update change log.** Record the deployment in `planning/change_log.md` with date and what was deployed.

8. **Dev/prod parity.** If this deployment adds new Cloud Run jobs or env vars, check that `infra/environments/app/prod/terraform.tfvars` is updated to match.

## When Things Go Wrong

- **Docker build fails:** Check the Dockerfile and build context. Common issues: missing dependencies, Python version mismatch, or stale `requirements.txt` / lockfile.
- **Migration fails on dev:** Do NOT attempt on prod. Examine the migration script for auto-generator template errors (a known pitfall — see lessons-learned). Write a manual migration if needed.
- **Cloud Run service crashes on startup:** Check logs with `/check-log`. Common causes: missing env var, Secret Manager permission, or import error not caught locally.
- **Smoke test fails locally:** Do not deploy. Diagnose with `/fix-bug` first.
