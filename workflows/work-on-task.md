---
agent: antigravity
description: "Execute a task with autonomous code editing, testing, and documentation."
---

# Work on Task

**Role: Autonomous Agent (Antigravity).** Execute a single implementation task, or a group of tasks in the same sprint (or phase), with proper testing and documentation.

## Steps

1. **Understand the task.** Use your `list_dir`, `grep_search`, and `view_file` tools to locate and read the relevant code. You are encouraged to scan the workspace to gain full context before making edits.
2. **Implement.** Use `replace_file_content` or `multi_replace_file_content` to write the code following the standards for the file type. Key principles:
   - Settings access via `get_settings()`, not hard-coded values
   - Stores via factories in `src/i4g/services/factories.py`
   - Type hints on every function, Google-style docstrings on public methods
   - Proactively review the language style guides in `antigravity/knowledge/` (e.g., Python, TypeScript).
3. **Test.** Write or update tests for the changed logic:
   - Use `run_command` to execute tests: `conda run -n i4g pytest tests/unit -x` (stop on first failure)
   - If adding env vars, add coverage under `tests/unit/settings/`
4. **Document.** Update documentation if behavior changed:
   - `docs/` if user-facing
   - Note in `planning/change_log.md`
   - **CRITICAL:** If working from a task plan (like `planning/tasks/*.md`), use `replace_file_content` to check off the completed tasks (`[ ]` to `[x]`). If untestable, mark as `[-]`.
5. **Validate locally.** Run pre-commit hooks to catch formatting issues early using `run_command`:
   `conda run -n i4g pre-commit run --files <changed-files>`
6. **Summarize.** Report what was done and the test results. **CRITICAL: Do NOT output the modified code or code diffs in your chat response. Simply list the files that were modified.**
