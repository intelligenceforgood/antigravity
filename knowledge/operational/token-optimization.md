# Token Optimization

> Operational knowledge for minimizing token spend while maximizing output quality across the I4G platform.

---

## Context Loading Best Practices

- **Check for digests first.** Always look in `planning/digests/` before reading raw source files. A digest is ~2K tokens vs ~50K+ for raw source.
- **Use `/context-map` output** (~4K tokens) instead of reading source files across repos. The context map provides cross-repo awareness without the cost.
- **Batch reads by component.** Read all files for one component/module together, then move on. Interleaving across repos causes redundant context reloads.
- **Extract facts into working notes on first read.** Write key findings (signatures, types, config values) into a scratch note. Never re-read a file to re-extract the same information.
- **Use `grep`/`find` with narrow queries.** Always constrain by path, file extension, and pattern. Never `grep -r` across an entire repo without filters.
- **Prefer targeted line-range reads.** Use `view_file` with `StartLine`/`EndLine` or `head`/`tail` instead of reading entire large files.

## Model-Specific Token Efficiency

### Planning Tier (Tier 1)
- **Highest intelligence, most resource-intensive.** Every token counts.
- **Current Model Mapping**: Gemini 3.1 Pro (High) / Opus (see `knowledge/standards/model-tiers.md` for updates).
- **Use for:** Architecture design, cross-repo planning, complex system design, sprint planning, feature decomposition.
- **Never use for:** Implementation, testing, diagnostics, log analysis, simple code changes.
- **Discipline:** Arrive with a clear question. Provide pre-loaded context (digests, context maps). Extract the plan, then switch to a cheaper tier to execute.

### Review Tier (Tier 2)
- **Strong reasoning at moderate resource cost.**
- **Current Model Mapping**: Gemini 3.1 Pro (High) / Sonnet (see `knowledge/standards/model-tiers.md` for updates).
- **Use for:** Code review, sprint wrapups, security audits, standards enforcement, complex debugging analysis.
- **Save tokens by:** Using `/lean-review` on the Execution Tier (Light) for simple reviews (formatting, linting, obvious bugs). Reserve Review Tier for reviews requiring architectural judgment.
- **Discipline:** Scope reviews to specific files/PRs. Don't ask Review Tier models to "review the whole codebase."

### Execution Tier (Light) (Tier 3)
- **Fast and unlimited. The workhorse.**
- **Current Model Mapping**: Gemini Flash (see `knowledge/standards/model-tiers.md` for updates).
- **Use for:** Implementation, bug fixes, TDD, log analysis, file generation, deployment checklists.
- **Trade-off:** Needs more explicit instructions. Keep tasks narrow and file-isolated. Provide exact file paths, function names, and expected behavior.
- **Discipline:** One task per prompt. Include the file path and relevant context inline. Don't assume it remembers prior turns deeply.

### Execution Tier (Heavy) (Tier 3.5)
- **Balanced intelligence and unlimited usage.**
- **Current Model Mapping**: Gemini 3.1 Pro (Low/Medium) (see `knowledge/standards/model-tiers.md` for updates).
- **Use for:** Complex multi-file implementations, cross-repo bug traces, refactors touching shared types.
- **More resource-intensive than Light** but handles multi-step reasoning and cross-file dependencies better.
- **Discipline:** Use when Execution Tier (Light) fails or when the task inherently spans multiple files/repos. Don't default to Heavy — start with Light and escalate.

## Prompt Engineering for Token Efficiency

- **Be specific.** Name exact files, functions, and line ranges. "Fix the `create_review` function in `core/src/api/reviews.py` lines 45-60" is far cheaper than "fix the review creation bug."
- **Avoid open-ended exploration.** "Investigate the codebase" or "understand how X works" burns massive tokens. Instead: "Read the digest for core, then tell me which function handles review creation."
- **Use skill invocations** (`/work-on-task`, `/implement-feature`) instead of free-form instructions. Skills have built-in token discipline — they know what to read, what to skip, and when to stop.
- **Decompose large requests.** Break "implement this feature" into: (1) plan the implementation, (2) implement module A, (3) implement module B, (4) write tests. Each becomes a focused, cheap prompt.
- **Include the project slice.** Specify which repos are relevant (e.g., "this only touches `core` and `ui`") to prevent unnecessary scanning of `ml`, `infra`, `mobile`, etc.
- **Front-load context.** Put the most important information (file paths, error messages, expected behavior) at the top of your prompt, not buried in a paragraph.

## Anti-Patterns (Token Wasters)

> [!CAUTION]
> Each of these patterns can waste 10K–100K+ tokens per occurrence. Avoid them rigorously.

| Anti-Pattern | Token Cost | Better Alternative |
|:---|:---|:---|
| Reading all files in a repo to "understand the codebase" | 50K–200K | Use digests + context maps (~6K) |
| Asking for code in chat output | 2K–10K per block | Use `write_to_file` / `replace_file_content` tools |
| Broad `find` or `grep` without path/type constraints | 5K–50K | Add `--include`, path filters, `MaxDepth` |
| Re-reading architecture docs loaded earlier in session | 3K–8K per read | Extract key facts to working notes on first read |
| Using Planning Tier for implementation tasks | 5× cost multiplier | Use Execution Tier (Light/Heavy) for implementation |
| Starting planning work when Planning Tier quota is exhausted | Wasted session | Check quota before starting; defer to next Planning Tier window |
| Asking "what changed?" without specifying a commit range | 10K–50K | Use `git diff <range>` or `git log -n 5 --oneline` |
| Iterating on a fix without reading the error message first | 5K–20K per attempt | Always read the full error, then fix once |

## Session Management

- **Plan sessions around model tiers:**
  1. **Planning Tier session** → Planning, architecture, task decomposition
  2. **Execution Tier session** → Implementation, testing, file generation
  3. **Review Tier session**    → Review, security audit, standards check
- **Use `/session-bridge`** to transfer state between sessions. This avoids re-reading files and re-establishing context.
- **Aim for 10–15 turns per session maximum.** Beyond this, context windows fill up and responses degrade.
- **Deep sessions (20+ turns) suffer from context pollution.** The model starts confusing earlier context with current state. Split proactively after completing a logical phase.
- **Start each session with a clear goal.** "This session: implement the review API endpoints per task T-042" is far more efficient than "let's continue working on the project."

## Decision Tree: Which Model for What?

| Task Type | Model | Rationale |
|:---|:---|:---|
| New feature architecture | Planning Tier | Cross-repo coherence needed |
| Implementation planning | Planning Tier | File mapping accuracy critical |
| Single-repo code changes | Execution Tier (Light) | Fast, cheap, sufficient for isolated work |
| Multi-repo code changes | Execution Tier (Heavy) | Handles cross-file complexity |
| Bug fix (isolated) | Execution Tier (Light) | Narrow scope, quick turnaround |
| Bug fix (cross-repo) | Execution Tier (Heavy) | Needs broader context awareness |
| Code review | Review Tier | Standards + security depth |
| Quick review (no Review Tier available) | Execution Tier (Light) via `/lean-review` | Automated checks only |
| Log analysis | Execution Tier (Light) | Pattern matching, no deep reasoning needed |
| Deployment | Execution Tier (Light) | Checklist execution |
| Security audit | Review Tier | Requires careful reasoning about attack vectors |
| Database migration | Execution Tier (Heavy) | Schema changes need cross-model awareness |
| Documentation | Execution Tier (Light) | Straightforward generation |
| Sprint planning | Planning Tier | Cross-repo task decomposition |

---

> [!TIP]
> **Rule of thumb:** If you're spending more than 3 turns loading context, you're doing it wrong. Use digests, context maps, and targeted reads to get oriented in 1 turn, then execute.
