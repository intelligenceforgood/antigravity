---
name: Architecture
description: Draft a technical architecture proposal ensuring compliance with I4G standards
model: Opus 4.6
---

# Architecture & Technical Design Proposal

**Role: Staff Engineer (Autonomous Agent).** Based on the attached PRD, outline a technical architecture.

## When to Use This Skill

- A PRD exists and the technical approach needs to be designed before breaking into tasks
- The user says "architecture", "technical design", "system design", or "design proposal"
- A feature spans multiple repos or requires new infrastructure components

## Before You Start

1. Read `antigravity/knowledge/architecture/architecture.md` to understand the existing platform.
2. Read the relevant language standards from `antigravity/knowledge/standards/`.

## Template

### 1. System Overview
High-level explanation of how this feature integrates into the existing systems.

### 2. Component Design
- Which components will be created/modified?
- Provide Mermaid flowcharts if applicable.

### 3. Data Model
- Database schema changes or new entities.

### 4. API Interface
- New endpoints, request/response models.

### 5. File Manifest for Execution
- Explicitly list the exact files that will need to be created or modified with full paths. This limits scope for the subsequent execution phase.

## Execution Rules

1. **Save the architecture doc** using `write_to_file` to `planning/architecture/` (create the directory if needed).
2. After generating, suggest the `/plan-work` skill as the next step.
3. Do NOT output the full document in chat — just confirm it was saved and summarize.
4. **Model Routing**: This is a planning-tier skill. Run this skill strictly on **Opus 4.6** to ensure architectural soundness. Do not use Gemini Pro due to trust/reasoning limits.
