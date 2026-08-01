---
description: Full test matrix execution — unit tests, build verification, infrastructure validation across all repos with coverage reporting
---

1. **Discover changed repos.** Run `git status --porcelain` and `git diff --name-only HEAD~1` in each of the 9 repo directories (`/Users/jerry/Work/project/i4g/{antigravity,core,ssi,ui,infra,ml,mobile,planning,docs}`). Build a list of repos that have uncommitted changes OR recent commits. Also note which repos have test suites by checking for `tests/`, `__tests__/`, `pytest` config, or `pnpm test`/`pnpm build` scripts. Summarize the discovery as a table: repo name, has changes (Y/N), has test suite (Y/N), test runner type.

// turbo

2. **Run Python test suites.** For each changed Python repo that has tests, execute the appropriate test command using the correct conda environment:
   - `core` → `conda run -n core pytest tests/unit -x --tb=short`
   - `ssi` → `conda run -n ssi pytest tests/unit -x --tb=short`
   - `ml` → `conda run -n ml pytest tests/unit -x --tb=short`
   Capture exit codes, test counts (passed/failed/skipped), and any failure output for each repo. Skip repos that have no changes or no test suite.

// turbo

3. **Run UI verification.** If `ui` has changes, run the following from `/Users/jerry/Work/project/i4g/ui`:
   ```
   pnpm lint && pnpm build
   ```
   Capture exit code and any lint errors or build failures. The successful build is the verification gate. If `ui` has no changes, note it as skipped.

// turbo

4. **Run infrastructure validation.** If `infra` has changes, run the following from `/Users/jerry/Work/project/i4g/infra`:
   ```
   terraform fmt -check -recursive
   terraform validate
   ```
   Capture exit codes and any formatting or validation errors. If `infra` has no changes, note it as skipped.

// turbo

5. **Compile results matrix.** Collect all results from steps 2–4 and present a structured pass/fail summary table with these columns:
   | Repo | Test Type | Status | Tests Passed | Tests Failed | Tests Skipped | Notes |
   Include rows for every repo that was tested, plus "skipped" rows for repos with no changes. Add a summary line at the bottom with overall pass/fail verdict. If any repo failed, highlight the failure details below the table.

// turbo

6. **Review results.** Present the full results matrix to the user for review. If all tests passed, confirm the green status. If any tests failed, list the specific failures with file paths and short error summaries. Wait for user direction before proceeding.

7. **Investigate or fix failures.** Based on user direction, either:
   - Investigate specific test failures by reading the failing test files and source code to identify root causes.
   - Propose targeted fixes with a file-level summary of what needs to change.
   - Re-run specific test suites after fixes are applied to verify the fix.
   Follow the propose-before-write protocol — summarize proposed fixes and wait for approval before modifying any files.

// turbo
