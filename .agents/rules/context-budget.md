---
description: Token budget discipline — progressive context loading to prevent mid-session exhaustion
activation: always_on
---

# Context Budget Discipline

> Apply these rules in every session to conserve the context window and prevent mid-session token exhaustion.

## Progressive Disclosure Protocol

When starting any planning or architecture task:

1. **Start with the context map.** If a context map exists for the active project slice (in `planning/context-maps/`), read it FIRST. It provides structural context at ~4K tokens vs ~50K+ for raw source files. If no map exists, invoke `/context-map` to generate one.

2. **Drill down selectively.** Only read full source files when:
   - The context map reveals an ambiguity that can't be resolved from signatures alone
   - You need to verify an implementation detail for a specific function
   - The user explicitly asks you to review specific code

3. **Batch reads by component.** When you must read source files, read all files for one component before moving to the next. Don't interleave reads across repos — this causes redundant context switching.

4. **Summarize and release.** After reading a file for context, extract the relevant facts into your working notes (artifact) and avoid re-reading the same file later in the session.

5. **Use search over read.** Prefer `grep`, `find`, and search tools over reading entire files. A targeted search that returns 5 matching lines is vastly cheaper than reading a 500-line file.

6. **Prefer targeted file updates.** Do not replace or overwrite whole files when modifying code. Strictly use targeted search-and-replace (`replace_file_content` / `multi_replace_file_content`) with minimal lines of context to conserve tokens.

## Anti-Patterns (Never Do)

- ❌ Reading all source files in a repo "to understand the codebase"
- ❌ Reading test files during planning (read them during verification only)
- ❌ Reading infrastructure code when working on application logic
- ❌ Reading full files when grep/search can answer the question
- ❌ Re-reading files already read earlier in the same session
- ❌ Reading `node_modules/`, `__pycache__/`, `.next/`, `dist/`, or `data/` directories
- ❌ Rewriting or outputting entire files in chat to apply small changes
- ❌ Keeping excessive unused editor tabs open (which automatically load into the agent's context in some environments)

## Project Slice Awareness

When the active project is a focused slice (e.g., `i4g-frontend`, `i4g-backend`), do NOT attempt to read or reference repos outside the slice. The slice boundary is intentional — trust it. If cross-repo context is genuinely needed, tell the user to switch to `i4g-full` or use `/context-map` output from a broader slice.

## Model-Specific Context Budgets

When running on fast/cheaper models (like Gemini 3.5 Flash (H/M/L) or Gemini 3.1 Pro (H/L)):
- Break edits into smaller, contiguous chunks and verify each chunk sequentially rather than doing massive multi-file updates.
- Keep output responses extremely brief. Rely on updated files on disk rather than long explanations.

## Subagent Token Hygiene

When delegating tasks via `invoke_subagent`:
- Prefer `share` workspace mode to avoid branch/clone disk and time overhead.
- Pass the narrowest possible brief. Only list the target files to modify and the specific standards relevant to the task.
- Avoid passing general codebase summaries or redundant instructions.
