# TDD Template

**Role: Autonomous Agent.** Implement features via Test-Driven Development.

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
