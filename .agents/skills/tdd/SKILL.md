---
name: TDD
description: Implement via Test-Driven Development (Red → Green → Refactor)
---

# TDD Template

**Role: Autonomous Agent.** Implement features via Test-Driven Development.

## When to Use This Skill

- The user explicitly requests TDD or test-first development
- Implementing logic with complex edge cases that benefit from tests before code
- The user says "TDD", "test-driven", or "write tests first"

## Before You Start

1. Read `antigravity/knowledge/standards/testing.md` and the relevant language standard.
2. Use `grep_search` or `list_dir` to locate the relevant source and test files.

## Steps

1. **Red Phase (Write Test):**
   - Write a failing test based on the requirements, ensuring it complies with testing standards.
   - Run the test to confirm it fails: `conda run -n i4g pytest <test_file> -x`

2. **Green Phase (Make it Pass):**
   - Write the minimal code required to pass the test.
   - Run the test to confirm it passes.
   - Do NOT output modified code in chat — just list the modified files.

3. **Refactor Phase:**
   - Refactor the code for clarity, performance, and adherence to the language-specific standards.
   - Ensure tests remain green.
   - Do NOT output refactored code in chat.

## When Things Go Wrong

- **Red phase test doesn't fail:** The test is not actually testing the intended behavior. Rewrite the assertion to target the specific functionality.
- **Green phase breaks other tests:** Your implementation has a side effect. Review the broader test suite, understand the dependency, and adjust the implementation.
- **Fixtures or mocking issues:** Check `antigravity/knowledge/operational/lessons-learned.md` for known pitfalls (e.g., SQLite NOT NULL constraints, mocking background tasks).
