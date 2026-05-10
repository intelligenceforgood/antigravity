---
agent: antigravity
description: "Diagnose and fix a reported bug autonomously."
---

# Fix Bug

**Role: Autonomous Agent (Antigravity).** Diagnose and fix the reported issue.

## Steps

1. **Scope Context:**
   - Analyze the stack trace or bug description provided by the user.
   - Use `grep_search` and `view_file` to find the specific files involved.
   - Proactively read the appropriate language style guide from `antigravity/knowledge/`.
   - If the bug relates to pipelines, deployment, or vulnerabilities, read `testing.md`, `ci-cd.md`, or `security.md`.
2. **Diagnose:** Identify the root cause.
3. **Execute Fix:** Use `replace_file_content` or `multi_replace_file_content` to implement the bug fix. Comply fully with the architectural and security standards.
4. **Verify:** Use `run_command` to run the relevant tests or server commands to verify the fix works.
5. **Summarize:** Provide a concise explanation of what was changed and why. **CRITICAL: Do NOT output the modified code or code diffs in your chat response. Simply list the files that were modified.**
