---
name: Code Review
description: Pre-merge review: cross-repo consistency, architecture, quality gates, tests
---

# Pre-Merge Code Review

**Role: Principal Engineer (Autonomous Agent).** Review the staged or modified changes against the implementation plan and I4G standards.

## When to Use This Skill

- Implementation is complete and changes need review before merging
- The user says "review", "code review", "check my changes", or "audit"
- Used as a sub-procedure by `/git-merge` and `/wrapup-and-merge`

## Before You Start

1. Run `git status` and `git diff` across all workspace repos to identify changes.
2. Read the relevant standards from `antigravity/knowledge/standards/` based on the file types in the diff.
3. Read `antigravity/knowledge/standards/security.md` and `antigravity/knowledge/standards/ci-cd.md`.

## 1. Multi-Repo Consistency & Dependencies (CRITICAL)

- **Identify Changed Repos:** Run `git status` in each workspace repo to determine which have staged changes.
- **Cross-Repo Dependencies:** Verify that API changes in one repo are correctly consumed in dependent repos. Changes must be merged simultaneously.
- **Architecture Compliance:** Check changes against `antigravity/knowledge/architecture/architecture.md`.

## 2. Documentation Synchronization

- Verify that code changes are synchronized with `docs/config/settings_manifest.yaml` and `planning/change_log.md`.

## 3. Architecture Consistency

- Does the code follow established patterns in the relevant language standards?

## 4. Coding Quality & Quality Gates

- Check for deviations from standards (type hints, naming conventions, docstrings).
- **Repo-Specific Gates:** Run quality gates:
  - Python: `conda run -n i4g pre-commit run --all-files`
  - UI: `cd ui/ && pnpm format && pnpm lint && pnpm build`

## 5. Test Coverage

- Is the changed code adequately covered by tests?
- Run tests to verify: `conda run -n i4g pytest tests/unit -x`

## 6. Action Items

List specific bugs, improvements, or "Looks good to merge". Keep code snippets minimal — describe fixes concisely rather than outputting whole files.
