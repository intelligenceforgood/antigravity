# Antigravity Workflow Framework

The centralized knowledge base, architectural guidelines, and standardized workflows for the I4G team's Antigravity AI coding assistant.

By maintaining these centrally in a version-controlled repository, we ensure consistent AI-driven development across all I4G multi-root repositories (`core`, `ui`, `ssi`, `infra`, `ml`, `planning`, `docs`, `mobile`).

---

## 🚀 Quick Start

**Invoke a workflow** by mentioning its file in your Antigravity prompt:

```
@[antigravity/workflows/planning/plan-work.md] Plan the new bulk export feature
```

**Browse available workflows** in the [Workflow Index](workflows/INDEX.md).

**Natural language** also works for common workflows:
- "plan work for X" → `plan-work.md`
- "work on task step 3" → `work-on-task.md`
- "fix the bug in X" → `fix-bug.md`
- "merge" → `git-merge.md`

---

## 📂 Repository Structure

```
antigravity/
├── workflows/                    # Standardized SDLC workflows
│   ├── INDEX.md                  # Complete catalog of all workflows
│   ├── planning/                 # PRD, architecture, implementation planning
│   │   ├── plan-work.md
│   │   ├── prd.md
│   │   └── arch.md
│   ├── execution/                # Task implementation, TDD, bug fixing
│   │   ├── work-on-task.md
│   │   ├── tdd.md
│   │   ├── fix-bug.md
│   │   └── clarify.md
│   ├── review/                   # Code review, log analysis, verification
│   │   ├── code-review.md
│   │   ├── check-log.md
│   │   └── manual-verification.md
│   └── lifecycle/                # Sprint wrapup, merge, deploy, lessons
│       ├── sprint-wrapup.md
│       ├── git-merge.md
│       ├── wrapup-and-merge.md
│       ├── deploy-to-dev.md
│       ├── record-lesson.md
│       └── hardening-sprint.md
├── knowledge/                    # Architectural standards & operational knowledge
│   ├── architecture/             # Platform architecture, routing rules
│   ├── standards/                # Python, TypeScript, testing, security, CI/CD, etc.
│   └── operational/              # Lessons learned, workflow discipline
├── docs/                         # Framework documentation
│   ├── workflow-dispatch.md      # How to invoke workflows
│   ├── user-rules-template.md    # Recommended user_rules for team consistency
│   ├── onboarding.md             # Developer onboarding guide
│   └── cookbook.md                # Real-world workflow examples
└── README.md
```

---

## 🧠 How It Works

### Knowledge (Auto-Loaded Context)

The `knowledge/` directory contains the I4G architectural standards, coding conventions, and operational knowledge. When Antigravity executes a workflow, it proactively reads the relevant knowledge files — **you don't need to manually load them**.

### Workflows (Executable Procedures)

Workflows are markdown files that Antigravity reads and treats as executable instructions. Each workflow defines a standardized procedure for a specific SDLC activity (planning, coding, reviewing, merging, etc.).

### User Rules (Global Behavior)

See `docs/user-rules-template.md` for the recommended `user_rules` configuration that ensures consistent agent behavior across all team members and sessions.

---

## 📖 Documentation

1. **[Workflow Index](workflows/INDEX.md)** — Complete catalog of all available workflows
2. **[Workflow Dispatch Guide](docs/workflow-dispatch.md)** — How to invoke workflows
3. **[User Rules Template](docs/user-rules-template.md)** — Recommended agent behavior rules
4. **[Onboarding Guide](docs/onboarding.md)** — Getting started for new developers
5. **[Cookbook](docs/cookbook.md)** — Real-world workflow examples and recipes
6. **[Routine Catalog](docs/routine-catalog.md)** — Detailed reference for every workflow
7. **[Customization Guide](docs/customization-guide.md)** — How to extend and maintain the framework

---

_For support, issues, or suggestions, open an issue in this repository._
