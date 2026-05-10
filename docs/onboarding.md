# Antigravity Onboarding Guide

Welcome to the **Antigravity** workflow for the I4G team! 

Antigravity is an autonomous, agentic coding assistant. Unlike our previous workflows (which relied heavily on manual VSCode snippets and manually tagging context files), Antigravity actively navigates our multi-root workspaces, reads files, executes shell commands, and autonomously applies architectural standards.

## 1. Starting a Session

- **When to start fresh:** Always start a new chat session when beginning an entirely new feature, or diagnosing an unrelated bug. This keeps the agent's active memory clear and focused.
- **When to stay in context:** If you are incrementally working through a plan (e.g., executing step 1, then step 2, then step 3), keep the same session open so the agent remembers the exact progress and context of the feature.

## 2. Workspace Navigation

You do not need to configure complex VSCode multi-root settings. Antigravity operates on absolute file paths and inherently understands our `ui`, `core`, `ssi`, and `planning` repositories. It can seamlessly read from and write to any of them in a single prompt.

## 3. The Knowledge Base

The `antigravity/knowledge/` directory contains our architectural styles, language constraints (e.g., `python.md`, `typescript.md`), and security rules.
- **You do not need to manually tag these files.**
- Antigravity is instructed to proactively scan and load these rules before executing code edits or planning sessions.

## 4. How to Execute Workflows

We use predefined markdown workflows located in `antigravity/workflows/` (such as `plan-work.md`, `work-on-task.md`, and `code-review.md`).

Instead of using snippets, simply instruct the agent in plain English to use the workflow.

**Examples:**
- *"Antigravity, please execute the `plan-work.md` workflow for the new search filtering feature described in planning/PRD."*
- *"We have a bug in the review queue. Please run the `fix-bug.md` workflow to diagnose and fix it. Here is the stack trace..."*
- *"Execute step 1 from the implementation plan using `work-on-task.md`."*

## 5. Reviewing Agent Actions

Because Antigravity is autonomous:
- **Code Edits:** The agent will directly edit files on your filesystem. You can use standard `git diff` to review its work.
- **Terminal Commands:** The agent will ask for your approval before running shell commands (like running `pytest` or `pre-commit`). Always verify the command is safe before hitting approve.
- **Artifacts:** For complex plans or reports, Antigravity will generate rich interactive artifacts in the chat interface rather than just dumping raw markdown.

---
*Ready to dive in? Check out the [Cookbook](cookbook.md) for concrete examples of daily tasks.*
