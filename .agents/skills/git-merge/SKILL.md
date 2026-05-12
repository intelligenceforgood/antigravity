---
name: Git Merge
description: Full review + commit + push across all changed repos
---

# Git Merge

**Role: Autonomous Agent.** Run the full pre-merge review, then commit and push all changed repos across the workspace to `main`. This is the single combined routine — no need to run `code-review` separately.

## When to Use This Skill

- Changes are ready to go to `main` and need to be committed and pushed
- The user says "merge", "commit and push", "ship it", or "push to main"
- Following successful code review or sprint wrapup

> **CRITICAL:** This is an executable routine. Execute these instructions autonomously.

## Phase 1 — Identify Changed Repositories

1. Scan the workspace using shell commands to find all git repositories with unstaged, staged, or unpushed changes:
   ```bash
   for repo in core ssi ui infra ml docs planning mobile antigravity; do
     dirty=$(cd /Users/jerry/Work/project/i4g/$repo && git status --porcelain 2>/dev/null)
     [[ -n "$dirty" ]] && echo "CHANGED: $repo" && echo "$dirty"
   done
   ```
2. List all identified changed repositories before proceeding.

## Phase 2 — Pre-Merge Review

For all changed repos identified in Phase 1:

1. Read and execute the code review procedure from the `/code-review` skill (`.agents/skills/code-review/SKILL.md`).
2. Strictly enforce all rules in `antigravity/knowledge/standards/ci-cd.md` and `antigravity/knowledge/standards/security.md`.
3. Actively look for scope drift: unrelated refactors, silent API changes, or unauthorized new dependencies.
4. Do NOT proceed to Phase 3 if any quality gate fails or tests have failures in ANY repo.

## Phase 3 — Clean Working Tree

Verify the working tree is merge-ready for EVERY changed repo:

1. **No stray files.** `git status` shows only intentional changes.
   - No merge conflict markers (verify via `git diff --check`)
   - No temp files (`*.tmp`, `*.bak`, `*.swp`, `.DS_Store`, `__pycache__/`)
   - No debug scripts outside `tests/` (`debug_*.py`, `scratch_*.py`)
   - If any found, alert the user and do NOT proceed

2. **No secrets.** Scan ALL pending changes for:
   - API keys / tokens (`sk-`, `ghp_`, `AIza`, `AKIA`)
   - Private keys (`BEGIN RSA PRIVATE KEY`, etc.)
   - `.env` files that should be gitignored
   - Hard-coded local paths (`/Users/<name>/`)
   - If any found, alert the user and do NOT proceed

## Phase 4 — Commit and Push

Follow `antigravity/knowledge/operational/merge-discipline.md` for all commit hygiene rules.

For each changed repo, execute:
```bash
cd <repo-root>
git add -A
git commit -m "<conventional commit message>"
git push origin main
```

- Use conventional commit format: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`
- Generate descriptive commit messages from the changes — do NOT ask the user to confirm
- This prompt constitutes explicit user instruction to push. You MUST execute `git push origin main` and MUST NOT ask for permission or leave it as a manual step.
- If push fails (e.g., diverged), alert the user — do NOT force push

## Phase 5 — Summary

Report per-repo: issues found + fixed, test results, files committed, commit hash, push status.
