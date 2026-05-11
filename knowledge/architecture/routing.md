# Workspace Context Routing

> **For the Antigravity Agent:** These are global rules that apply to ALL work across the I4G workspace.

You are operating in a multi-root workspace containing multiple independent Git repositories (`core/`, `ui/`, `ssi/`, `ml/`, `infra/`, `planning/`, `docs/`, `mobile/`, etc.).

## Critical Rules

1. **Repo-Specific Context:** When working in a specific repository, check for repo-specific rules or context files in that repo's root directory. Each repo may have its own conventions that supplement the global standards.

2. **Output Discipline:** Never output full file replacements or large code blocks in chat responses. When executing a task or updating files, simply list the files that were modified.

3. **Cross-Repo Awareness:** When a change in one repo requires corresponding changes in another (e.g., a Core API change requires a UI proxy update), proactively identify and make both changes. Do not leave the system in an inconsistent state.
