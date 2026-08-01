---
description: Commit and merge hygiene rules for all I4G repos
activation: always_on
---

# Merge Discipline

> Apply these rules during ALL commit, merge, and multi-phase skill operations.

## 1. Format Before Staging (UI Repo)

When editing JSON, TS, or TSX files, always run the formatter **before** `git add`:

```bash
cd ui/ && pnpm format
git add -A
git commit -m "..."
```

Skipping this causes the committed content to differ from Prettier — the next `pnpm format` run re-formats the file and leaves a dirty working tree.

## 2. Build Before Committing Config Changes

Any edit to `tsconfig.json`, `package.json`, build config, or settings files must pass the full build **before** `git commit`:

```bash
# UI
cd ui/ && pnpm build

# Python
cd core/ && conda run -n core pre-commit run --all-files
```

## 3. Post-Push Cleanliness Sweep

After pushing, run `git status -sb` in **every** workspace repo — not just the repos you changed:

```bash
for repo in antigravity core ssi ui infra ml docs planning mobile; do
  dirty=$(cd /Users/jerry/Work/project/i4g/$repo && git status --porcelain 2>/dev/null)
  [[ -n "$dirty" ]] && echo "DIRTY: $repo" && echo "$dirty"
done
```

If any repo is dirty, diagnose whether it's a formatter artifact (commit it) or an unintended change (revert it) before declaring the merge complete.

## 4. Conventional Commits

Use conventional commit format: `feat:`, `fix:`, `chore:`, `docs:`, `refactor:`, `test:`, `ci:`. Scope to the affected module when possible (e.g., `fix(api): handle null review status`).

## 5. Security Scan Before Push

Before every push, scan staged files for:
- API keys or tokens
- Private keys (`.pem`, `.key`)
- `.env` files or hard-coded secrets
- Hard-coded local paths (e.g., `/Users/jerry/...`)

If any are found, **stop and alert the user**.
