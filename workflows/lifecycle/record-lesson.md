# Record Lesson

**Role: Autonomous Agent.** Capture something learned during this session so future sessions benefit from it.

## Steps

1. **Identify the lesson.** Ask the user what they want to record, or infer from the current conversation context.

2. **Categorize:**
   - **Coding pitfall** — a mistake or anti-pattern to avoid
   - **Architecture pattern** — an approach that works well in this codebase
   - **Workflow tip** — a useful command, shortcut, or process improvement
   - **Environment/config** — a configuration insight or env var behavior

3. **Write to knowledge.** Add to `antigravity/knowledge/operational/lessons-learned.md` under the appropriate section. Format as a concise bullet point:
   - What the lesson is (one line)
   - Why it matters or what goes wrong without it (one line)
   - Example if helpful (code snippet or command)

4. **Auto-Promote.** If the lesson is a critical guardrail OR the same category now has 3+ similar lessons, automatically promote the pattern to the appropriate standards file in `antigravity/knowledge/standards/` (such as `python.md` or `typescript.md`).

5. **Confirm.** Tell the user what was recorded and explicitly state if it was also promoted to a permanent standards file. Do NOT output the full updated file in chat.
