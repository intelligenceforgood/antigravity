---
name: Delegate
description: Decompose a task plan into scoped subagent delegations with structured handoffs and verification loops
---

# Delegate — Subagent Task Handoff

**Role: Autonomous Orchestrator.** Take a task plan (from `planning/tasks/`) and decompose it into scoped work units that can be delegated to implementation subagents. Manage the full lifecycle: scope → brief → execute → verify → accept.

## When to Use This Skill

- A task plan exists with multiple independent work units across different repos
- The user says "delegate", "fan out", "parallelize", "hand off to subagents"
- You're in a planning session (Planning Tier) and need implementation work done by execution agents
- A task has clear, testable acceptance criteria and bounded file scope

## Before You Start

1. Read the task plan file from `planning/tasks/`.
2. Run `/context-map` if no recent map exists for the active slice.
3. Verify each task in the plan has explicit file paths — if not, fill them in before delegating.

## Handoff State Machine

```
SCOPE → BRIEF → EXECUTE → VERIFY → ACCEPT
  ↑                          │
  └──── (on failure) ────────┘
```

## Steps

### 1. SCOPE — Analyze & Partition

Review the task plan and partition into delegation units. Each unit must satisfy:
- **File isolation**: No two units modify the same file
- **Repo coherence**: Each unit touches at most 2–3 repos
- **Testability**: Each unit has at least one automated verification command
- **Independence**: Units can execute in parallel without coordination

**Classify each unit's complexity:**
- 🟢 **SIMPLE**: Single repo, follows existing patterns, no API/schema changes → Execution Tier (Light)
- 🟡 **MODERATE**: 1–2 repos, multiple files, may touch API routes → Execution Tier (Heavy)
- 🔴 **COMPLEX**: Cross-repo contracts, schema changes, new patterns → Escalate to orchestrator (do NOT delegate)

Present the partition as a table:

| Unit | Repos | Files to Modify | Complexity | Recommended Model | Acceptance Test | Parallelizable? |
|------|-------|----------------|:---:|:---:|-----------------|:---:|

Flag any tasks that CANNOT be parallelized (shared file dependencies) and sequence them.
🔴 COMPLEX units must NOT be delegated — they stay with the orchestrator (Planning Tier).

### 2. BRIEF — Compose Handoff Prompts

For each delegation unit, compose a structured brief using this exact template:

```markdown
## Delegation Brief — Unit <N>: <title>

**Task**: <description from plan>
**Project Slice**: <which i4g-* project this maps to>
**Complexity**: 🟢 SIMPLE / 🟡 MODERATE
**Recommended Model**: Execution Tier (Light) / Execution Tier (Heavy)

### Token Budget
- **Max context reads**: 5 files (read digest first if available in planning/digests/)
- **Max output lines**: 200 lines of code changes
- **Response format**: Use file-writing tools exclusively. Do NOT output code in chat.

### Context Files (read these first)
- planning/digests/<repo>-digest.md (if exists — use instead of raw source exploration)
- <relevant standards file from antigravity/knowledge/standards/>
- <task plan file, specific task numbers>

### Scope Boundary
**Modify these files:**
- <exact file paths>

**Do NOT modify (contract boundary):**
- <files that must remain unchanged>

### Implementation Instructions
<specific technical guidance — patterns to follow, functions to call, etc.>

### Acceptance Criteria
1. <test command that must pass>
2. <standards check>
3. No new imports from repos outside the scope boundary
4. All new public functions have Google-style docstrings
5. No hardcoded secrets or local paths

### Model Routing
Use **Execution Tier** for this implementation task. Planning Tier is reserved for planning and architectural decisions only.

### Completion Report Format
When done, respond with ONLY this structured summary (max 10 lines):
- Files modified: <list>
- Tests run: <command> → <pass/fail>
- Issues encountered: <none or description>
- Scope violations: <none or description>
Do NOT include code snippets, full file contents, or verbose explanations.
```

### 3. EXECUTE — Invoke Subagents

**Prefer specialized subagents over generic ones.** Use AG2's `define_subagent` tool to create repo-specific agents with targeted system prompts and knowledge. This reduces per-subagent token overhead by pre-loading only the relevant standards.

**Recommended subagent definitions (define once per session, reuse across delegations):**

| Subagent Name | System Prompt Should Include | Use For |
|:---|:---|:---|
| `core_impl` | `core/AGENTS.md` + `antigravity/knowledge/standards/python.md` | Backend API changes in `core/` |
| `ssi_impl` | `ssi/AGENTS.md` + `antigravity/knowledge/standards/python.md` | SSI agent/adapter changes in `ssi/` |
| `ui_impl` | `ui/AGENTS.md` + `antigravity/knowledge/standards/typescript.md` | Frontend/React changes in `ui/` |
| `infra_impl` | `infra/AGENTS.md` + `antigravity/knowledge/standards/terraform.md` | Infrastructure changes in `infra/` |

If a delegation unit spans repos not covered above, fall back to the generic `self` subagent type.

For **parallelizable units**: invoke all subagents simultaneously using `invoke_subagent`. Each subagent:
- Gets `enable_write_tools: true`
- Workspace mode: `inherit`
- Receives the delegation brief as its prompt
- Has a descriptive `Role` (e.g., "Core Backend Implementer", "UI Route Builder")

For **sequential units**: invoke one at a time, waiting for completion before the next.

Monitor subagents via `send_message` only if they haven't reported back within a reasonable time.

**Context folding**: When subagents report back, extract only the structured summary (files modified, test results, issues). Discard verbose explanations. This prevents orchestrator context bloat when managing multiple subagents.

### 4. VERIFY — Review Results

When a subagent reports completion:

1. **Run acceptance tests**:
   ```bash
   <the test commands from the acceptance criteria>
   ```

2. **Check scope compliance**:
   ```bash
   git diff --name-only  # Verify only scoped files were modified
   ```

3. **Check standards**:
   - No bare `except:` blocks
   - Docstrings on public functions
   - No hardcoded secrets (grep for `sk-`, `ghp_`, `AIza`, `AKIA`)
   - No hardcoded local paths (grep for `/Users/`)

4. **If verification fails**: Send feedback to the subagent with specific failure details. The subagent retries. Maximum 2 retries before escalating to user.

5. **If verification passes**: Mark the delegation unit as complete. Proceed to ACCEPT.

### 5. ACCEPT — Present for User Approval

Once all delegation units are verified:

1. **Summarize changes** (panoramic format — do NOT dump code):
   - Components affected
   - Net effect of all changes
   - Cross-repo implications
   - Test results

2. **Present uncommitted diff summary**:
   ```bash
   git diff --stat  # Per repo
   ```

3. **Prompt**: "All delegation units verified. Ready to commit. Run `/git-merge` to proceed, or review specific files first?"

## Execution Rules

1. **Never delegate without a task plan.** The plan is the contract. If no plan exists, invoke `/plan-work` first.
2. **Never delegate architecture or planning work.** Subagents implement; they don't design. Keep all design decisions in the planning session.
3. **Scope boundaries are hard limits.** If a subagent needs to modify a file outside its scope, it must report back — the orchestrator decides whether to expand scope or create a new delegation unit.
4. **Model routing**: Implementation subagents use Execution Tier. Planning and architecture stay on Planning Tier. This preserves Planning Tier quota for high-value decisions.
5. **Maximum fan-out**: No more than 4 concurrent subagents to avoid overwhelming verification.
