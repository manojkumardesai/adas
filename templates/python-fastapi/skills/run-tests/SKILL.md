---
name: run-tests
description: "Run the pytest test suite and report results. Use when executing tests, checking coverage, verifying changes pass tests, or debugging test failures."
---

# Run Tests

## When to Use
- After making code changes to verify nothing is broken
- When writing new tests and need to run them
- When debugging test failures
- When checking test coverage

## Procedure
1. Run the full suite: `uv run pytest -v`
2. For a specific file: `uv run pytest tests/test_users.py -v`
3. For coverage: `uv run pytest --cov=app --cov-report=term-missing`
4. If tests fail:
   - Read the failure output to identify which tests failed
   - Check if failure is in new code or existing tests
   - Read the failing test and source code
   - Suggest or apply fixes
5. Report: pass count, fail count, failed test names, coverage if requested
