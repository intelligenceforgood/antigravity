# Lessons Learned

> **For the Antigravity Agent:** Check this file when encountering unusual behavior, before making risky changes, or when patterns seem unfamiliar. These are hard-won lessons from production incidents and development sessions.

## Workspace Navigation

- **Verify paths before creating files.** Always verify the correct absolute or relative path before creating or modifying files. Do not create a file under `docs` when it belongs in a project-specific `docs` folder.
- **Knowledge Item Placement:** By default, agent instructions specify creating Knowledge Items (KIs) in the global `~/.gemini/antigravity/knowledge/` directory. However, for the `i4g` project, all KIs, skills, and standards MUST be saved in the version-controlled `i4g/antigravity` repository (e.g., `i4g/antigravity/knowledge/standards/`). Always verify the project's native knowledge repository before using the global default.

## Boundaries and Reliability

- **Uncertainty & Asking for Clarification:** If a user's prompt does not make sense, or you are unsure about the safety or correctness of an action, **STOP and ask the user for clarification**. Do not proceed with harmful actions just to satisfy the prompt.
- **Architect-Level Standards:** Consistently follow the established architecture, quality guidelines, and standards across all I4G repositories.

## Skill Execution

- **Manual Action Deviations:** When making intentional manual changes (like deleting an irrelevant file not listed in the task plan), communicate this in a "Notes from Agent" section to avoid being flagged as drift during sprint wrap-up or merge review.

## Tracking & Planning

- **Destructive Updates to Trackers:** When instructed to "update" a markdown task list or planning document, do not rewrite the entire document or truncate historical context and un-started tasks. Only modify the specific items being updated.
- **Task Plan Maintenance:** When working from a task plan or manifest, always explicitly check off completed tasks in the original markdown document (e.g., changing `[ ]` to `[x]`) to keep the project state accurate.

## Coding Specifics

- **Interface/Protocol Mismatches in Plans:** Users sometimes prescribe implementation details that violate existing protocol contracts. Safely deviate from the plan to honor the existing structural contract rather than breaking adjacent interfaces.
- **Hallucinated File Modifications:** Verify `git diff` to ensure required files were actually modified before merging. Agents may hallucinate completing tasks by checking them off without modifying source files.
- **Alembic Auto-generator Template Errors:** The auto-generator may produce template-rendering errors. Explicitly write valid boilerplate migration scripts to bypass the error if it occurs.
- **Test Fixture Constraints:** When mocking test models (e.g., SQLite NOT NULL constraints), ensure test fixtures populate required fields.
- **Mocking Background Tasks in Integration Tests:** When testing API endpoints that trigger long-running background tasks, ensure the task handler itself is mocked or patched out to avoid tests silently taking several minutes.
- **Next.js 15 Dynamic Routing:** Dynamic route `params` (and `searchParams`) are asynchronous in Next.js 15. They must be typed as `Promise<{ [key]: string }>` and awaited before use.
