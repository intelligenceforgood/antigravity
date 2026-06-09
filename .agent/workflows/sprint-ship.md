---
description: The "big button" — drift check, changelog, code review, secret scan, commit, and push across all changed repos
---

1. **Scan all 9 repos for dirty / unstaged / unpushed changes.**
   For each repo in `antigravity core ssi ui infra ml mobile planning docs`:
   - Run `git status --porcelain` to detect dirty or unstaged files.
   - Run `git log @{u}.. --oneline 2>/dev/null` to detect unpushed commits.
   - Produce a summary table: repo | branch | dirty files | unpushed commits | status (clean / needs-attention).
   - If ALL repos are clean, report "Nothing to ship" and stop.
// turbo

2. **Drift check — detect unrelated refactors, silent API changes, and unauthorized new dependencies.**
   For every repo flagged as dirty or unpushed in Step 1:
   - `git diff` and `git diff --cached` to collect all changed hunks.
   - Flag any changes that look unrelated to the current sprint work (random refactors, TODO removals, formatting-only edits in untouched modules).
   - Flag any new imports of external packages not already in `pyproject.toml`, `package.json`, or `requirements.txt`.
   - Flag any modified API route signatures, protocol/interface definitions, or shared type changes that could break cross-repo contracts.
   - Record each finding with severity (warning / blocker).
// turbo

3. **Update `planning/change_log.md` with a dated summary of all changes.**
   - Append a new section headed `## YYYY-MM-DD — Sprint Ship` (today's date).
   - Under the heading, list every repo that has changes with a one-line summary per changed file or logical group of changes.
   - Follow append-only rules — never rewrite or truncate existing entries.
// turbo

4. **Pre-merge code review — architecture, quality gates, test coverage.**
   For each dirty repo:
   - **Architecture:** Verify changes align with `antigravity/knowledge/architecture/architecture.md`. Flag layer violations, wrong-direction dependencies, or misplaced logic.
   - **Quality gates by repo type:**
     - Python repos (`core`, `ssi`): Run `conda run -n <env> pre-commit run --all-files` (two-pass: run once, `git add -u`, run again — must exit clean).
     - UI repo: Run `cd ui/ && pnpm format`, then `git add -A`, then `pnpm lint`, then `pnpm build`. All must pass.
     - Infra repo: Run `terraform fmt -check -recursive`. Must pass.
   - **Test coverage:** Check that new or modified modules have corresponding test files. Flag untested additions.
   - **Merge-discipline compliance:** For UI, confirm format-before-stage. For config changes, confirm build-before-commit.
   - Record each finding with severity (info / warning / blocker).
// turbo

5. **Secret scan on all staged and unstaged changes.**
   Scan every changed file across all repos for:
   - API keys, tokens, bearer strings, passwords in plain text.
   - Private key files (`.pem`, `.key`, `.p12`).
   - `.env` files or `.env.*` variants.
   - Hard-coded local paths (e.g., `/Users/jerry/`).
   - Cloud credentials, service account JSON, or connection strings.
   If ANY secret is detected, mark the gate as **BLOCKER** and list each finding with file path and line number.
// turbo

6. **Clean working tree check.**
   Across all 9 repos, verify:
   - No merge conflict markers (`<<<<<<<`, `=======`, `>>>>>>>`) in any tracked file.
   - No temporary files (`*.tmp`, `*.bak`, `*.swp`, `__pycache__/`, `.DS_Store`, `node_modules/` committed).
   - No debug scripts, `console.log` spam, `breakpoint()`, or `import pdb` left in committed code.
   - Working tree is in a committable state (no partial merges, no detached HEAD).
   Record each finding with severity (warning / blocker).
// turbo

7. **Present panoramic review summary — HALT for user sign-off.**
   Produce a structured sign-off artifact containing:
   - **Ship manifest:** table of repos → files changed → commit message (proposed conventional commit).
   - **Gate results:** pass/fail for each gate (drift, quality, secrets, cleanliness) per repo. List any warnings or blockers.
   - **Cross-repo impact:** any shared-type or API contract changes and which downstream repos are affected.
   - **Risk assessment:** breaking changes, migration needs, data impact.
   - If ANY gate is **BLOCKER**, state clearly that shipping is blocked until resolved.
   - End with: **"All gates passed. Ready to commit and push. Approve?"** (or the blocker summary).

8. **Commit all changed repos with conventional commit messages.**
   For each repo with changes:
   - Stage all changes (`git add -A`). For UI, run `pnpm format` first per merge-discipline.
   - Commit with the conventional commit message approved in Step 7.
   - Use `--no-verify` only if pre-commit was already run and passed in Step 4.
   - Verify `git diff --cached` is empty after commit (all changes captured).
// turbo

9. **Push all committed repos to origin.**
   For each repo that was committed in Step 8:
   - Run `git push origin <branch>` (NEVER `--force`).
   - If push fails (rejected, conflict, auth error): **STOP immediately**, report the failure with the full error message, and do NOT continue pushing other repos. Let the user resolve.
   - If push succeeds, record the commit hash and push status.
// turbo

10. **Post-push cleanliness sweep across ALL 9 repos.**
    Run the sweep from merge-discipline.md §3:
    ```bash
    for repo in antigravity core ssi ui infra ml docs planning mobile; do
      dirty=$(cd /Users/jerry/Work/project/i4g/$repo && git status --porcelain 2>/dev/null)
      [[ -n "$dirty" ]] && echo "DIRTY: $repo" && echo "$dirty"
    done
    ```
    If any repo is dirty after push, diagnose whether it's a formatter artifact (commit and push it) or an unintended change (revert it). Do not declare complete until all repos are clean.
// turbo

11. **Final per-repo summary.**
    Present a summary table:
    | Repo | Branch | Files Committed | Commit Hash | Push Status | Post-Push Clean? |
    For each repo, fill in the row. If a repo had no changes, mark as "—".
    Confirm: **"Sprint shipped. All repos clean."** or flag any remaining issues.
// turbo
