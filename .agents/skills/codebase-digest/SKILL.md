---
name: Codebase Digest
description: Generate persistent, version-controlled structural digests for each repo in the active project slice. Produces individual per-repo digest files in planning/digests/.
---

# Codebase Digest

You are a **codebase indexer**. You generate compact structural digests for each repo in the active project slice and save them as version-controlled files. Unlike `/context-map` (which runs live and produces a combined map), this produces individual per-repo digest files optimized for token-efficient context loading.

## When to Use This Skill

- User says "digest", "update digests", "refresh digests"
- Periodically after major changes or weekly maintenance
- Before a planning session to ensure digests are current
- When onboarding to a repo that lacks a recent digest

## Before You Start

- Identify the **active project slice** (which repos to digest). If unclear, ask the user.
- Check `planning/digests/` for existing digests. If a digest exists and is **<7 days old**, ask the user before regenerating.
- Create `planning/digests/` if it doesn't exist.
- This skill is **READ-ONLY** for source code — never modify source files.

## Steps

### Step 1 — Check existing digests

```
ls -la planning/digests/*-digest.md 2>/dev/null
```

For each file found, check the `Generated:` timestamp. If any digest is less than 7 days old, list it and ask: "These digests are recent. Regenerate anyway?"

If the user confirms or no recent digests exist, proceed.

### Step 2 — For each repo, extract public API signatures

Extract function/class signatures only (name + params + return type, NO bodies).

**Python repos** (`core`, `ssi`, `ml`):
```bash
grep -rn 'def \|class ' <repo>/src --include='*.py'
```
Then extract just the signature line. Strip docstrings, bodies, and decorators. Keep only:
- `def function_name(param: Type, ...) -> ReturnType`
- `class ClassName(BaseClass)`

**TypeScript repos** (`ui`):
```bash
grep -rn 'export.*function\|export.*const.*=\|export.*interface\|export.*type\|export.*class' <repo>/src --include='*.ts' --include='*.tsx'
```

Collapse to one line per signature. Omit internal/private functions (prefixed with `_`).

### Step 3 — Extract model/type definitions

**Python**: Grep for SQLModel, BaseModel, or Base subclasses:
```bash
grep -rn 'class.*\(.*Model\)\|class.*\(.*Base\)' <repo>/src --include='*.py'
```
For each model class, extract field names and types only (no validators, no methods).

**TypeScript**: Grep for interface/type definitions:
```bash
grep -rn 'export.*interface\|export.*type' <repo>/src --include='*.ts' --include='*.tsx'
```

### Step 4 — Extract route tables

**Python** (FastAPI): Grep for route decorators:
```bash
grep -rn '@app\.\(get\|post\|put\|delete\|patch\)\|@router\.\(get\|post\|put\|delete\|patch\)' <repo>/src --include='*.py'
```
Extract: HTTP method, path, handler function name.

**TypeScript** (Next.js): Find API route files:
```bash
find <repo>/src/app/api -name 'route.ts' -o -name 'route.tsx' 2>/dev/null
```
Extract exported HTTP method handlers (GET, POST, PUT, DELETE, PATCH).

### Step 5 — Extract key config

Grep for environment variables and settings:
```bash
grep -rn 'I4G_\|SSI_' <repo>/src --include='*.py' --include='*.ts' --include='*.tsx' | grep -v '__pycache__'
grep -rn 'class.*Settings' <repo>/src --include='*.py'
```
List unique env var names and Settings class fields.

### Step 6 — Compose and save the digest

For each repo, write a digest file to `planning/digests/<repo-name>-digest.md` with this format:

```markdown
# <Repo Name> Digest
Generated: <ISO 8601 timestamp>

## Public API
| Function/Class | File | Signature |
|:---|:---|:---|
| function_name | path/to/file.py | `def function_name(param: Type) -> ReturnType` |

## Data Models
| Model | File | Fields |
|:---|:---|:---|
| ModelName | path/to/models.py | field1: Type, field2: Type |

## Routes
| Method | Path | Handler | File |
|:---|:---|:---|:---|
| GET | /api/v1/resource | get_resource | path/to/routes.py |

## Config
| Variable | File | Context |
|:---|:---|:---|
| I4G_DATABASE_URL | path/to/settings.py | Database connection string |
```

**Target: <2K tokens per repo digest.** Collapse aggressively:
- Omit trivial getters/setters
- Group related endpoints (e.g., CRUD for one resource on one line)
- Abbreviate long type signatures
- If a section has no entries, write "None found" and move on

### Step 7 — Report results

After all repos are digested, report:
- Number of repos digested
- File paths created/updated (as clickable links)
- Estimated total token count across all digests
- Any repos that were skipped (and why)

## Execution Rules

- Save each repo digest as a **SEPARATE** file in `planning/digests/`
- Create the `planning/digests/` directory if it doesn't exist
- If a digest exists and is <7 days old, **ask before regenerating**
- This is **READ-ONLY** for source code — never modify any source files
- Keep each digest under **2K tokens** — summarize aggressively
- Use `grep` and `find` with narrow patterns — never read entire source trees
- Report: number of repos digested, total estimated tokens, and file paths
- **Automate with `/schedule`**: Instead of remembering to run this skill weekly, the user can set up a recurring schedule with AG2's built-in `/schedule` command: `/schedule: Run /codebase-digest every Monday at 9am`. This ensures digests are always fresh without manual intervention.
