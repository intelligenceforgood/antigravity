---
agent: antigravity
description: "Pre-merge code review — autonomously verifies cross-repo consistency, architecture, and tests"
---

# Pre-Merge Code Review

**Role: Principal Engineer (Autonomous Agent).** Review the staged or modified changes against the original implementation plan and the relevant standards.

## Steps

1. **Identify Changes:** Use the `run_command` tool to execute `git diff` or `git status` to find modified/staged files across the workspace (e.g., `core/`, `ssi/`, `ui/`, `infra/`).
2. **Check Knowledge:** Based on the file extensions found, proactively read the corresponding styleguides from `antigravity/knowledge/` (e.g., `python.md`, `typescript.md`).
3. **Multi-Repo Consistency:** Verify that architectural changes or API updates in one repo are correctly integrated and consumed in dependent repos.
4. **Architecture Consistency:** Ensure the code follows the established patterns. Check `antigravity/knowledge/architecture.md`.
5. **Quality Gates:** Ensure quality gates pass by using `run_command` (e.g., Python pre-commit double-pass, UI `make check`/`make build`).
6. **Action Items:** List specific bugs, improvements, or "Looks good to me". Proactively offer to use your file editing tools to fix any identified issues. Do not print out whole files in chat.
