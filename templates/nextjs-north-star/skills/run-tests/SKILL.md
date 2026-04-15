---
name: run-tests
description: "Run the test suite for this Next.js project. Use when checking if tests pass, running tests after changes, or getting a coverage report."
---
# Run Tests

Runs the Vitest test suite and reports results.

## Commands

### Unit and component tests
```bash
pnpm test
```

### With coverage
```bash
pnpm test -- --coverage
```

### Watch mode (during development)
```bash
pnpm test -- --watch
```

### Single file or directory
```bash
pnpm test -- features/user-profile
```

### E2E tests (Playwright)
```bash
pnpm test:e2e
```

## Interpreting Results
- All tests must pass before opening a PR
- Coverage target: >80% for `features/` and `lib/`
- E2E tests run in CI against a preview deployment

## After Failures
1. Read the failure output carefully — Vitest shows the diff
2. Check if the test is asserting wrong behavior (fix the test) or the code broke (fix the code)
3. For Server Action test failures: verify mocks match the actual module exports
4. For component failures: check if the component's accessible name matches the query
