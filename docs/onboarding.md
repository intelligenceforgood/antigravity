# Antigravity Developer Onboarding

Welcome to the **Antigravity Skill Framework** for the I4G team. This guide walks you through setup, core concepts, and your first skill.

## Prerequisites

- **Antigravity** installed and authenticated.
- Access to the I4G Git repositories (`core`, `ui`, `ssi`, `infra`, `ml`, `planning`, `docs`, `mobile`).
- The `antigravity` repository cloned locally and accessible on your machine.

## Step 1: Workspace Configuration

Antigravity operates on absolute file paths and natively supports multi-root workspaces. 

To configure your environment, simply add all relevant I4G repos (including the `antigravity` repo) to your current IDE workspace. Antigravity will automatically detect and navigate across all of them, regardless of where they are physically stored on your machine.

## Step 2: Configure Your User Rules

For consistent agent behavior across all sessions, add the recommended rules to your Antigravity settings. See [docs/user-rules-template.md](user-rules-template.md) for the full template.

Key rules to add:
- **Skill awareness** — the agent will check for relevant skills before starting work
- **Knowledge loading** — the agent will proactively read architectural standards before coding
- **Output discipline** — the agent won't dump large code blocks in chat
- **Planning discipline** — the agent will track task progress in plan files

These rules persist across ALL conversations — you only set them once.

## Step 3: Understand the Framework

### Knowledge (Automatic Context)

The `antigravity/knowledge/` directory contains architectural standards, coding conventions, and operational lessons. When Antigravity executes a skill, it proactively reads the relevant knowledge files. **You never need to manually load these.**

```
knowledge/
├── architecture/    # Platform architecture, routing, auth, database schema
├── standards/       # Python, TypeScript, testing, security, CI/CD, Docker, model tiers
└── operational/     # Lessons learned, merge discipline, skill execution rules
```

### Skills (Executable Procedures)

The `.agents/skills/` directory contains standardized procedures for every stage of development. Browse the [Skill Catalog](skill-catalog.md) to see all available skills.

```
.agents/skills/
├── plan-work/       # Implementation planning
├── prd/             # Generate PRDs
├── work-on-task/    # Task implementation
├── fix-bug/         # Bug fixing
└── ... (and more)
```

## Step 4: Invoke Your First Skill

There are two ways to invoke a skill:

### Method 1: Slash Command (Recommended)

Use the slash command in your prompt:

```
/plan-work Plan the new user search feature
described in planning/prd_user_search.md
```

Antigravity reads the skill and follows its procedure autonomously.

### Method 2: Natural Language Shorthand

For common skills, just describe what you want:

```
Plan work for the new search filtering feature
```

The agent recognizes the intent and follows the appropriate skill. See the [Skill Catalog](skill-catalog.md) for all available shorthands.

## Step 5: Understanding Agent Autonomy

Antigravity is an **autonomous agent** — it reads files, writes code, and runs commands on your behalf. Here's what to expect:

### What the agent does automatically
- Reads source code, knowledge files, and plan documents
- Writes and edits code files
- Generates implementation plans and saves them to `planning/tasks/`
- Checks off completed tasks in plan files
- Records changes in `planning/change_log.md`

### What requires your approval
- **Shell commands** — the agent proposes commands (like `pytest`, `git push`, `pnpm build`) and waits for you to approve before executing
- **Destructive operations** — file deletions, force pushes, or database operations are flagged

### How to review its work
- Use `git diff` to see what the agent changed
- Check `planning/tasks/` for updated plan checklists
- Check `planning/change_log.md` for documented changes

## Step 6: Your First Skill (Try It)

1. **Plan a feature:**
   ```
   /plan-work Plan a small improvement:
   add a "last updated" timestamp to the case detail page in the UI
   ```

2. **Execute step 1:**
   ```
   /work-on-task Work on task Step 1 from
   planning/tasks/<the-plan-file-created-above>.md
   ```

3. **Review the code:**
   ```
   /code-review Review my staged changes
   ```

4. **Merge:**
   ```
   /git-merge Merge the changes
   ```

## Session Management Tips

- **Stay in the same session** when working through a multi-step plan (Step 1, Step 2, Step 3...). The agent remembers context within a session.
- **Start a new session** when switching to a completely different feature or bug.
- **Don't worry about "rehydrating"** — Antigravity's Knowledge Items and conversation logs persist across sessions. Unlike GCA, there's no need to manually reload context at the start of each session.

---

## Step 7: Token Economy & Model Routing

To maximize development efficiency and minimize token costs (especially on restricted developer subscription tiers like AI Pro), Antigravity standardizes on a **tiered model routing strategy**. See `knowledge/standards/model-tiers.md` for the full tier definitions and current model mappings.

- **Planning Tier**: Reserve the highest reasoning model for structural planning skills (`/prd`, `/arch`, `/plan-work`). Do not use Execution Tier models for architecture or planning due to reasoning limitations.
- **Review Tier**: Use for high-value `/code-review` tasks, security audits, and sprint wrapups.
- **Execution Tier**: Switch your active model to fast, low-cost, high-context models for `/work-on-task`, `/fix-bug`, `/tdd`, and operational commands.
  - Use **Execution Tier (Light)** for standard coding, unit testing, and utility execution.
  - Use **Execution Tier (Heavy)** when resolving complex multi-file bugs or troubleshooting detailed logs.
- **Utility Tier**: Model-agnostic skills like `/session-bridge`, `/record-lesson`, and `/context-map` run equally well on any model.

By executing Execution Tier tasks on cheaper models, you conserve premium model quotas for critical structural design decisions.

---

Ready to dive deeper? Check the [Cookbook](cookbook.md) for real-world skill recipes.
