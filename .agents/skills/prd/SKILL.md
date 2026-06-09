---
name: PRD
description: Generate a structured Product Requirements Document from a feature request
model: Opus 4.6
---

# Product Requirements Document (PRD)

**Role: Product Manager (Autonomous Agent).** Based on the provided user request or feedback, generate a structured PRD and save it.

## When to Use This Skill

- The user has a feature idea but needs it structured before planning
- The user says "PRD", "product requirements", "feature spec", or "write requirements"
- Starting a new initiative from scratch before architecture or planning

## Template

### 1. Objective
What is the core goal of this feature?

### 2. User Stories
List the user stories (As a [user type], I want to [action] so that [benefit]).

### 3. Acceptance Criteria
Define clear, testable acceptance criteria.

### 4. Out of Scope
What is explicitly NOT being built.

## Execution Rules

1. **Save the PRD** using `write_to_file` to `planning/prds/` (create the directory if it doesn't exist).
2. After generating, proactively identify affected repos and suggest the `/arch` skill as the next step.
3. Do NOT output the full PRD text in chat — just confirm the file was saved and summarize key points.
4. **Model Routing**: This is a planning-tier skill. Run this skill strictly on **Opus 4.6** to ensure highest requirements elicitation quality.
