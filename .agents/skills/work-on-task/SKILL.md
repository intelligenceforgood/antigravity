---
name: Work on Task
description: Implement a task from a plan with testing, documentation, and validation
model: execution-tier
---

# Work on Task

**Role: Autonomous Agent.** Execute a single implementation task, or a group of tasks in the same sprint (or phase) with proper testing and documentation.

## When to Use This Skill

- A task plan exists in `planning/tasks/` and specific steps need to be implemented
- The user says "work on", "implement", "execute step", or "do task"
- Following up on a `/plan-work` output to execute the actual code changes

## Before You Start

1. **Check for repo digests**: If `planning/digests/<repo>-digest.md` exists for the target repo, read it FIRST. The digest provides function signatures, model definitions, and route tables at ~2K tokens vs ~50K+ for raw source. Only read full source files that the specific task targets.
2. Read the relevant standards from `antigravity/knowledge/standards/` based on the file types you'll be modifying.
3. If unfamiliar with the codebase area, scan the target directory to understand existing patterns — but limit to `list_dir` and targeted `grep`, not full file reads.

## Steps

1. **Understand the task.** Read the task plan and the relevant source code. Don't modify code you haven't read. Use `view_file`, `list_dir`, and `grep_search` to gain full context. **File read budget: max 5 source files per task** unless the task plan explicitly lists more. If you need broader context, re-read the digest instead of opening more files.

2. **Implement.** Write the code following the standards for the file type. Key principles:
   - Settings access via `get_settings()`, not hard-coded values
   - Stores via factories in `src/i4g/services/factories.py`
   - Type hints on every function, Google-style docstrings on public methods
   - Specific exception handling (no bare `except:`)
   - Follow the rules in the relevant language standards (Python, TypeScript/React)

3. **Test.** Write or update tests for the changed logic:
   - Unit tests under `tests/unit/`
   - Run tests: `conda run -n core pytest tests/unit -x` (stop on first failure)
   - If adding env vars, add coverage under `tests/unit/settings/`
   - Run any relevant verification commands to ensure the change works

4. **Document.** If behavior changed:
   - Update `docs/` if user-facing
   - Update config manifests if env vars changed
   - Note in `planning/change_log.md`
   - **CRITICAL:** If working from a task plan, check off completed tasks (`[ ]` → `[x]`). If a task isn't testable yet (lack of API key, etc.), mark as `[-]`.

5. **Validate locally.** Run pre-commit hooks:
   ```bash
   conda run -n core pre-commit run --files <changed-files>
   ```

6. **Summarize.** Report what was done, tests that pass, and any follow-up items. Do NOT output modified code in chat — just list the modified files.

7. **Session depth check.** If this task was preceded by 10+ turns in the current session, proactively suggest: "Consider running `/session-bridge` before continuing with the next task."

8. **Model Routing & Token Economy**:
   - Run this execution-tier skill on **Execution Tier (Light)** (standard coding tasks) or **Execution Tier (Heavy)** (complex multi-file adjustments). Do not run execution on Planning Tier models to conserve quota.
   - Strictly practice targeted editing: use `replace_file_content` / `multi_replace_file_content` with anchor context lines to edit target segments. Avoid full file replacements.

## When Things Go Wrong

- **Tests fail after your change:** Examine the failure. If it's a pre-existing failure unrelated to your change, note it and proceed. If your change caused it, fix before continuing.
- **Import or dependency error:** Check `pyproject.toml` / `package.json`. If a new dependency is needed, install it and document in the plan.
- **Blocked by missing context or ambiguity:** Invoke the `/clarify` skill instead of guessing.
- **Task is untestable** (e.g., requires API key or running service): Mark the task as `[-]` in the plan, note the blocker, and move on.
