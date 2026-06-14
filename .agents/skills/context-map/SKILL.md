---
name: Context Map
description: Generate a condensed architecture map of the active project slice for token-efficient planning
---

# Context Map — Token-Efficient Architecture Scanner

**Role: Autonomous Agent.** Scan the active workspace repos and produce a condensed structural map that captures file trees, API surfaces, data models, and cross-repo contracts — without reading implementation bodies. This map replaces the need to ingest full source files during planning.

## When to Use This Skill

- Starting any planning, architecture, or design session
- The user says "context map", "scan workspace", "map the codebase", or "what's in scope"
- Before invoking `/arch`, `/plan-work`, or the feature-planning workflow
- When switching to a new project slice and needing orientation

## Before You Start

1. Identify which repos are in the active project slice (check the conversation's project context).
2. Read `antigravity/knowledge/architecture/architecture.md` for the platform reference — do NOT re-extract what's already documented there.
3. **Check for existing digests**: If `planning/digests/<repo>-digest.md` exists for repos in the slice and is <7 days old, incorporate digest data instead of re-scanning those repos. This avoids redundant extraction.
4. **Delta mode**: If a previous context map exists for this slice (in `planning/context-maps/`), check `git status --porcelain` across all slice repos. Only re-scan repos with uncommitted changes. Reuse the previous map's data for unchanged repos.

## Steps

### 1. File Tree Extraction

For each repo in the active slice, run:

```bash
find <repo_path>/src -type f \( -name "*.py" -o -name "*.ts" -o -name "*.tsx" -o -name "*.tf" \) \
  ! -path "*/node_modules/*" ! -path "*/__pycache__/*" ! -path "*/.next/*" \
  ! -path "*/dist/*" ! -path "*/.git/*" ! -path "*/data/*" ! -path "*/.venv/*" \
  | head -200
```

Collapse into a directory tree showing folder structure and file counts per directory. Do NOT list every individual file — summarize: `api/  # 12 route files`.

### 2. API Surface Extraction

For **Python repos** (core, ssi, ml):
```bash
grep -rn '@\(app\|router\)\.\(get\|post\|put\|delete\|patch\)' <repo_path>/src --include="*.py" | head -50
```

For **TypeScript repos** (ui, mobile):
```bash
find <repo_path> -path "*/app/api/*/route.ts" -o -path "*/app/api/*/route.tsx" | head -30
```

For **Terraform repos** (infra):
```bash
grep -rn '^resource\|^module\|^data' <repo_path> --include="*.tf" | head -30
```

Present as a table: `METHOD /path → file:function()`.

### 3. Type/Model Extraction

For **Python repos**:
```bash
grep -rn 'class .*\(SQLModel\|BaseModel\|Base\):' <repo_path>/src --include="*.py" | head -30
```

For each model class, extract field names only (not types or validators):
```bash
grep -A 20 'class ModelName' <file> | grep -E '^\s+\w+\s*[:=]' | head -15
```

For **TypeScript repos**:
```bash
grep -rn 'interface\|type .* =' <repo_path>/src --include="*.ts" --include="*.tsx" | head -30
```

### 4. Cross-Repo Import Detection

```bash
# In ssi: imports from core's namespace
grep -rn 'from i4g\.' <ssi_path>/src --include="*.py" | head -10

# In ui: SDK client usage
grep -rn 'i4g-client\|resolveClient\|I4G_API_URL' <ui_path>/src --include="*.ts" --include="*.tsx" | head -10

# Shared env vars across repos
grep -rn 'I4G_\|SSI_' <repo_path> --include="*.py" --include="*.ts" --include="*.tf" | grep -v node_modules | head -20
```

### 5. Assembly

Combine all findings into a single markdown document with these sections:

```markdown
# Context Map: <project-slice-name>
Generated: <ISO timestamp> | Repos: <list>

## File Tree (filtered)
<collapsed directory trees per repo>

## API Surface
<tables per repo>

## Data Models
<class names + field lists, no method bodies>

## Cross-Repo Contracts
<imports, shared env vars, API consumption patterns>

## Key Architectural Notes
<anything surprising or important for planning>
```

**Target size: under 3,000 tokens.** If the output exceeds this, further collapse by:
- Removing obvious/boilerplate entries
- Grouping similar endpoints (e.g., "CRUD on /cases" instead of listing all 5 endpoints)
- Showing only public-facing models (skip internal DTOs)
- Referencing digest files instead of duplicating their content: "See `planning/digests/core-digest.md` for full API surface"

## Execution Rules

1. **Save the map** to `planning/context-maps/<slice-name>-<YYYYMMDD>.md`. Create the directory if it doesn't exist.
2. Do NOT output the full map in chat — confirm it was saved, state the token count estimate, and summarize the key findings in 3–5 bullets.
3. If a recent map exists (< 7 days old) for the same slice, ask the user whether to regenerate or reuse.
4. This skill is **read-only** — it must never modify source code, only read and report.
5. **Use the `research` subagent for background scanning.** When generating a context map for a large slice (4+ repos), consider invoking AG2's built-in `research` subagent to scan repos in parallel. The research subagent is read-only by design and returns summarized findings — a natural fit for context extraction.
