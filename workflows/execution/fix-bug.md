# Fix Bug

**Role: Autonomous Agent.** Diagnose and fix the reported issue autonomously.

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

4. **Verify:**
   - Run relevant tests to verify the fix: `conda run -n i4g pytest tests/unit -x`
   - Provide a concise explanation of what was changed and why.
   - Do NOT output modified code in chat — just list the modified files.
   - Provide a run command to test the fix if applicable.
