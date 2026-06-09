---
name: Delegate
description: Decompose a task plan into scoped subagent delegations with structured handoffs and verification loops
---

# Delegate — Subagent Task Handoff

**Role: Autonomous Orchestrator.** Take a task plan (from `planning/tasks/`) and decompose it into scoped work units that can be delegated to implementation subagents. Manage the full lifecycle: scope → brief → execute → verify → accept.

## When to Use This Skill

- A task plan exists with multiple independent work units across different repos
- The user says "delegate", "fan out", "parallelize", "hand off to subagents"
- You're in a planning session (Opus) and need implementation work done by execution agents
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

Present the partition as a table:

| Unit | Repos | Files to Modify | Acceptance Test | Parallelizable? |
|------|-------|----------------|-----------------|-----------------|

Flag any tasks that CANNOT be parallelized (shared file dependencies) and sequence them.

### 2. BRIEF — Compose Handoff Prompts

For each delegation unit, compose a structured brief using this exact template:

```markdown
## Delegation Brief — Unit <N>: <title>

**Task**: <description from plan>
**Project Slice**: <which i4g-* project this maps to>

### Context Files (read these first)
- antigravity/knowledge/architecture/architecture.md
- <relevant standards file>
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
Use **Gemini** for this implementation task. Opus is reserved for planning and architectural decisions only.
```

### 3. EXECUTE — Invoke Subagents

For **parallelizable units**: invoke all subagents simultaneously using `invoke_subagent` with multiple entries in the `Subagents` array. Each subagent:
- Gets `enable_write_tools: true`
- Workspace mode: `inherit`
- Receives the delegation brief as its prompt
- Has a descriptive `Role` (e.g., "Backend API Implementer", "Frontend Route Builder")

For **sequential units**: invoke one at a time, waiting for completion before the next.

Monitor subagents via `send_message` only if they haven't reported back within a reasonable time.

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
4. **Model routing**: Implementation subagents use Gemini. Planning and architecture stay on Opus. This preserves Opus quota for high-value decisions.
5. **Maximum fan-out**: No more than 4 concurrent subagents to avoid overwhelming verification.
