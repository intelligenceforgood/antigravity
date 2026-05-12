---
name: Manual Verification
description: Post-deployment verification: health checks, API smoke tests, UI verification
---

# Manual Verification

**Role: Autonomous Agent.** Verify a deployment is working correctly after pushing to an environment.

## When to Use This Skill

- After deploying to dev or prod and need to verify everything works
- The user says "verify", "smoke test", "check the deployment", or "is it working"
- Following a `/deploy-to-dev` execution to confirm success

## Steps

1. **Determine environment.** Ask the user which environment to verify (dev or prod). Default to dev.

2. **Health checks.** Verify core services are running:
   - Core API: hit the health/readiness endpoint via `run_command` (curl)
   - Check Cloud Run service logs for startup errors or crash loops

3. **API smoke tests.** Test key API endpoints:
   - `GET /reviews/search` — search should return results
   - `GET /reviews/{id}` — single review lookup
   - `GET /tasks/{task_id}` — task status (if background jobs are relevant)

4. **UI verification.** If UI was deployed:
   - Load the analyst console
   - Verify search works end-to-end (UI → API proxy → FastAPI → ReviewStore)
   - Check that recent data is visible

5. **Worker/job verification.** If worker jobs were deployed:
   - Trigger a test job (or check recent job execution logs)
   - Verify job completion and output artifacts

6. **Compare with expectations.** Based on what was changed, verify:
   - New features are accessible
   - Fixed bugs are resolved
   - No regressions in adjacent functionality

7. **Report.** Summarize: what was verified, pass/fail for each check, any issues found.

8. **Sign off or escalate.** If everything passes, confirm the deployment is good. If issues are found, recommend next steps (rollback, hotfix, or investigation).
