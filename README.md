# Antigravity Skill Framework

The centralized knowledge base, architectural guidelines, and standardized skills for the I4G team's Antigravity AI coding assistant.

By maintaining these centrally in a version-controlled repository, we ensure consistent AI-driven development across all I4G multi-root repositories (`core`, `ui`, `ssi`, `infra`, `ml`, `planning`, `docs`, `mobile`).

---

## 🚀 Quick Start

**Invoke a skill** using its slash command:

```
/plan-work Plan the new bulk export feature
```

**Browse available skills** in the [Skill Catalog](docs/skill-catalog.md).

**Natural language** also works for common skills:
- "plan work for X" → `/plan-work`
- "work on task step 3" → `/work-on-task`
- "fix the bug in X" → `/fix-bug`
- "merge" → `/git-merge`

---

## 📂 Repository Structure

```
antigravity/
├── .agents/skills/                # Standardized SDLC skills (Antigravity native)
│   ├── plan-work/SKILL.md         # Implementation planning
│   ├── prd/SKILL.md               # Product requirements documents
│   ├── arch/SKILL.md              # Architecture & technical design
│   ├── work-on-task/SKILL.md      # Task implementation
│   ├── tdd/SKILL.md               # Test-driven development
│   ├── fix-bug/SKILL.md           # Bug diagnosis & fixing
│   ├── clarify/SKILL.md           # Structured clarification requests
│   ├── code-review/SKILL.md       # Pre-merge code review
│   ├── check-log/SKILL.md         # GCP Cloud Run log diagnosis
│   ├── manual-verification/SKILL.md # Post-deployment verification
│   ├── sprint-wrapup/SKILL.md     # End-of-sprint documentation
│   ├── git-merge/SKILL.md         # Cross-repo commit & push
│   ├── wrapup-and-merge/SKILL.md  # Combined wrapup → review → merge
│   ├── deploy-to-dev/SKILL.md     # Pre-flight checklist & deploy
│   ├── record-lesson/SKILL.md     # Capture lessons learned
│   └── hardening-sprint/SKILL.md  # Platform hardening sessions
├── knowledge/                     # Architectural standards & operational knowledge
│   ├── architecture/              # Platform architecture, routing rules
│   ├── standards/                 # Python, TypeScript, testing, security, CI/CD, etc.
│   └── operational/               # Lessons learned, merge discipline
├── docs/                          # Framework documentation
│   ├── using-skills.md            # How to invoke skills
│   ├── skill-catalog.md           # Complete catalog of all skills
│   ├── user-rules-template.md     # Recommended user_rules for team consistency
│   ├── onboarding.md              # Developer onboarding guide
│   ├── customization-guide.md     # How to extend and maintain the framework
│   └── cookbook.md                 # Real-world skill recipes
└── README.md
```

---

## 🧠 How It Works

### Knowledge (Auto-Loaded Context)

The `knowledge/` directory contains the I4G architectural standards, coding conventions, and operational knowledge. When Antigravity executes a skill, it proactively reads the relevant knowledge files — **you don't need to manually load them**.

### Skills (Executable Procedures)

Skills are SKILL.md files inside `.agents/skills/` that Antigravity reads and treats as executable instructions. Each skill defines a standardized procedure for a specific SDLC activity (planning, coding, reviewing, merging, etc.). They are a native Antigravity capability — invoked via `/slash-commands` or natural language.

### User Rules (Global Behavior)

See `docs/user-rules-template.md` for the recommended `user_rules` configuration that ensures consistent agent behavior across all team members and sessions.The 

---

## 📖 Documentation

1. **[Skill Catalog](docs/skill-catalog.md)** — Complete catalog of all available skills
2. **[Using Skills](docs/using-skills.md)** — How to invoke skills
3. **[User Rules Template](docs/user-rules-template.md)** — Recommended agent behavior rules
4. **[Onboarding Guide](docs/onboarding.md)** — Getting started for new developers
5. **[Cookbook](docs/cookbook.md)** — Real-world skill recipes
6. **[Customization Guide](docs/customization-guide.md)** — How to extend and maintain the framework

---

_For support, issues, or suggestions, open an issue in this repository._
