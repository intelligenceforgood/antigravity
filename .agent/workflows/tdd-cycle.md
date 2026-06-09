---
description: TDD workflow — Red (failing test) → Green (minimal pass) → Refactor, with review gates at each phase
---

# TDD Cycle

Test-Driven Development workflow with structured review gates at each phase transition.

## Context Boundary

Focused on the target repo, with standards loaded from:
- `antigravity/knowledge/standards/testing.md` — testing conventions
- `antigravity/knowledge/standards/python.md` or `typescript.md` — language-specific rules
- `antigravity/knowledge/operational/lessons-learned.md` — known test pitfalls (fixtures, mocking, async params)

Environment routing:
| Repo | Test Command |
|------|-------------|
| `core` | `conda run -n i4g pytest tests/unit -x --tb=short` |
| `ssi` | `conda run -n i4g-ssi pytest tests/unit -x --tb=short` |
| `ml` | `conda run -n ml pytest tests/unit -x --tb=short` |
| `ui` | `cd ui && pnpm test` |
| `mobile` | `cd mobile && pnpm test` |

## Steps

1. **Load context.** Read `antigravity/knowledge/standards/testing.md` and the relevant language standard. Locate existing test files for the target module using `find` and `grep`. Identify the test directory structure, existing fixtures, and naming conventions already in use.
// turbo

2. **Understand the requirement.** Read the feature spec, task plan, or user description. Identify the specific behavior to test. Determine the function/class/endpoint under test, the expected inputs and outputs, and edge cases.
// turbo

3. **🔴 Red Phase — Write the failing test.** Write a test that captures the desired behavior following the standards from Step 1. Run it to confirm it **fails**:
   ```bash
   conda run -n <env> pytest <test_file>::<test_name> -x --tb=short
   ```
   - If the test **passes** unexpectedly: the test is not targeting the right behavior. Rewrite the assertion — do NOT proceed to Green with a passing test.
   - Present: test file path, test function name, assertion logic, and the failure output.
   
   **Wait for user to review the test design before proceeding to Green.**

4. **🟢 Green Phase — Make it pass.** Write the **minimal** code needed to make the test pass. No refactoring, no optimization, no extra features — just enough to turn red to green. Run the specific test:
   ```bash
   conda run -n <env> pytest <test_file>::<test_name> -x --tb=short
   ```
   Then run the broader test suite to check for regressions:
   ```bash
   conda run -n <env> pytest tests/unit -x --tb=short
   ```
   - If the broader suite **breaks**: investigate the side effect. Fix the regression before proceeding — do NOT leave broken tests.
   - Report: modified files (no code in chat), test pass status, regression check results.
// turbo

5. **Green phase review.** Present the Green phase results:
   - Test passing confirmation
   - Regression check results (all other tests still green)
   - List of modified files
   
   **Wait for user review before proceeding to Refactor.**

6. **🔄 Refactor Phase.** Refactor the implementation for:
   - Clarity: readable variable names, clear control flow
   - Standards compliance: type hints, docstrings, `get_settings()` usage, specific exceptions
   - Performance: obvious inefficiencies only (no premature optimization)
   
   After each refactor, confirm all tests remain green:
   ```bash
   conda run -n <env> pytest tests/unit -x --tb=short
   ```
// turbo

7. **Pre-commit validation.** Run quality gates on all changed files:
   - **Python**: `conda run -n <env> pre-commit run --files <changed-files>` (two-pass: run, `git add -u`, run again)
   - **UI**: `pnpm format && pnpm lint`
// turbo

8. **Final summary.** Report:
   - Modified files (source + test)
   - All test results (specific test + full suite)
   - Any follow-up items (additional test cases, integration tests, documentation)
   - If working from a task plan, check off the completed task (`[ ]` → `[x]`)
// turbo
