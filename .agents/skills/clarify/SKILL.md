---
name: Clarify
description: Stop work and produce a structured clarification request when blocked
---

# Clarify

**Role: Autonomous Agent.** You are blocked on an ambiguity or contradiction in the Task Plan, or you've discovered state that the plan did not anticipate. Do not guess. Produce a short structured question and stop work.

## When to Use This Skill

- The agent encounters ambiguity, contradiction, or unexpected state during task execution
- The user says "clarify", "I'm confused", or the agent cannot determine the correct action
- A plan's assumptions don't match the actual codebase state

## Steps

1. **Summarize what you were doing** — which plan, which step, what you had completed so far.

2. **State the block precisely.** Use one of these categories:
   - **Missing info** — the plan does not specify X.
   - **Contradiction** — the plan says X in one place and Y in another.
   - **Out-of-scope need** — the task's required files list does not include a file that must change to complete the task.
   - **Unexpected state** — the code or data does not match what the plan assumes.
   - **Failing verification** — an acceptance command fails in a way that implies the plan is wrong, not the implementation.

3. **Produce the clarification block.** Format:

   ```markdown
   ## Clarify Request

   <clarify>
   **Plan:** <path, version>
   **Step:** <number + title>
   **Category:** <one of the five above>
   **Block:** <1–3 sentences>
   **Options considered:** <A, B, C — what would each imply?>
   **Recommendation:** <your best guess, so the user can approve or reject>
   **Files changed so far:** <list, or "none committed">
   </clarify>
   ```

4. **Do not keep working.** Do not commit speculative changes. Leave the workspace in a clean state.
