---
name: Fix Bug
description: Diagnose and fix a reported bug autonomously
model: Gemini 3.5 Flash (H/M) / Gemini 3.1 Pro (High)
---

# Fix Bug

**Role: Autonomous Agent.** Diagnose and fix the reported issue autonomously.

## When to Use This Skill

- A bug report, stack trace, or error description is provided
- The user says "fix", "debug", "diagnose", or references a production error
- The `/check-log` skill identified a code issue that needs fixing

## Before You Start

1. Read the relevant language standards from `antigravity/knowledge/standards/`.
2. If the bug relates to testing, deployment, or security, also read `testing.md`, `ci-cd.md`, and/or `security.md`.

## Steps

1. **Scope Context:**
   - Analyze the stack trace or error description provided by the user.
   - Use `grep_search` and `view_file` to locate the relevant source files.
   - If the bug spans multiple repos, identify all affected files.

2. **Diagnose:**
   - Review the relevant code and identify the root cause.
   - If possible, use `run_command` to reproduce the bug locally.

3. **Execute Fix:**
   - Implement the bug fix following the architectural and coding standards.
   - Ensure the fix addresses the root cause, not just the symptom.

4. **Verify**:
   - Run relevant tests to verify the fix: `conda run -n i4g pytest tests/unit -x`
   - Provide a concise explanation of what was changed and why.
   - Do NOT output modified code in chat — just list the modified files.
   - Provide a run command to test the fix if applicable.
   - **Model Routing & Token Economy**:
     - Run standard bugs on **Gemini 3.5 Flash (H/M)**. For complex multi-file bugs or stack trace log analysis, use **Gemini 3.1 Pro (High)**.
     - Strictly practice targeted editing: use `replace_file_content` / `multi_replace_file_content` with anchor context lines to edit target segments. Avoid full file replacements.

## When Things Go Wrong

- **Cannot reproduce:** If the bug only occurs in cloud, check environment-specific config (`I4G_ENV`, service URLs, secret values). Note the limitation and propose a cloud-only verification step.
- **Fix causes new test failures:** Investigate whether the test assumptions are outdated. Fix the test if it's testing the wrong behavior; otherwise revert and reconsider the approach.
- **Root cause is in a different repo:** Identify the upstream repo, make the fix there, and note the cross-repo dependency. Both changes must ship together.
- **Unclear root cause:** If diagnosis takes more than 2 read-through cycles without progress, invoke the `/clarify` skill to ask the user for more context.
