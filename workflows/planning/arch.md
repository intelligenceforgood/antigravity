# Architecture & Technical Design Proposal

**Role: Staff Engineer (Autonomous Agent).** Based on the attached PRD, outline a technical architecture.

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
2. After generating, suggest the `plan-work` workflow as the next step.
3. Do NOT output the full document in chat — just confirm it was saved and summarize.
