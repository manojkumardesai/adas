---
name: run-tests
description: "Run the Vitest test suite and report results. Use when executing tests, checking coverage, verifying changes pass tests, or debugging test failures."
---

# Run Tests

## When to Use
- After making code changes to verify nothing is broken
- When writing new tests and need to run them
- When debugging test failures
- When checking test coverage

## Procedure
1. Run the test suite: `npm test`
2. For a specific file: `npx vitest run {path}`
3. For coverage: `npx vitest run --coverage`
4. If tests fail:
   - Read the failure output to identify which tests failed
   - Check if failure is in new code or existing tests
   - Read the failing test file and the source code it tests
   - Suggest or apply fixes
5. Report: pass count, fail count, failed test names, coverage if requested
