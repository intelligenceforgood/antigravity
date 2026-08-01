# Merge & Commit Discipline

> **For the Antigravity Agent:** Apply these rules during ALL commit, merge, and multi-phase skill operations.

Hard-won rules for the commit-and-push phase of the merge skill.

## 1. Format before staging (UI repo)

When editing JSON, TS, or TSX files, the output often diverges from Prettier's canonical formatting. Always run the formatter **before** `git add`:

```bash
cd ui/ && pnpm format        # Prettier writes canonical output
git add -A                   # now safe to stage
git commit -m "..."
```

Skipping this causes the committed content to differ from Prettier — the next `pnpm format` run re-formats the file and leaves a dirty working tree.

## 2. Build before committing config changes

Any edit to `tsconfig.json`, `package.json`, build config, or settings files must pass the full build **before** `git commit`:

```bash
# UI
cd ui/ && make build

# Python
cd core/ && conda run -n core pre-commit run --all-files
```

## 3. Post-push cleanliness sweep

After pushing, run `git status -sb` in **every** workspace repo — not just the repos you changed. Formatters, editors, and hooks can silently modify files after a commit.

```bash
for repo in antigravity core ssi ui infra ml docs planning mobile; do
  dirty=$(cd /Users/jerry/Work/project/i4g/$repo && git status --porcelain 2>/dev/null)
  [[ -n "$dirty" ]] && echo "DIRTY: $repo" && echo "$dirty"
done
```

If any repo is dirty, diagnose whether it's a formatter artifact (commit it) or an unintended change (revert it) before declaring the merge complete.

## 4. Executing Multi-Phase Skills

When instructed to execute a multi-phase skill, **do not pause and ask for permission** to proceed to the next phase unless the skill explicitly instructs you to do so. Complete all phases autonomously.

## 5. Interpreting Skill Files as Actions

When a user provides a skill file alongside a target plan or file, you must **execute the instructions** within that skill file against the workspace. Act upon the instructions autonomously rather than literally appending the content into another document.
