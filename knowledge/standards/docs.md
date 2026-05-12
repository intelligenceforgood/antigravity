# Documentation & GitBook Discipline

> **For the Antigravity Agent:** Apply these rules when working with documentation in the `docs/` repository.

## Canonical GitBook Formatting Reference

GitBook provides an official skill file at `docs/skill.md` (the `docs` workspace repo root). **Always read `docs/skill.md` directly** when you need GitBook syntax reference — custom blocks, frontmatter fields, variables, configuration files, etc.

Do NOT rely on any cached or copied version of this file. The canonical source is always `docs/skill.md`.

## I4G Documentation Rules

1. **Location:** All end-user and structural documentation lives in `docs/book/` and its subdirectories (e.g., `docs/book/analyst-guide/`, `docs/book/key-concepts/`). Do not drop `.md` files in the root of the `docs/` repo.

2. **Table of Contents:** GitBook relies on `docs/book/SUMMARY.md` to build its navigation. Any new Markdown file created in the `docs/book/` directory **must** be added to the appropriate section in `docs/book/SUMMARY.md`. If a file is not in `SUMMARY.md`, it is orphaned and invisible to the user.

3. **Internal Linking:** Use relative paths matching the structure defined in `SUMMARY.md`.

4. **Read SUMMARY.md First:** When working with existing docs content, always read `docs/book/SUMMARY.md` first to understand the navigation structure before making changes.

5. **Images & Assets:** Store in `docs/.gitbook/assets/`. Reference as `![alt](../.gitbook/assets/image-name.svg)`.

6. **Git Sync:** GitBook auto-syncs with the repo. Make structural changes (navigation) through `SUMMARY.md` in Git. Content changes can go through either Git or the GitBook UI, but be consistent within a session.
