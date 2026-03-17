---
description: "Use when writing or modifying test files. Covers Vitest patterns, React Testing Library conventions, mocking, and test organization for this project."
applyTo: "**/*.test.ts,**/*.test.tsx"
---
# Testing Conventions

## Framework
- Test runner: Vitest
- React testing: React Testing Library
- Run tests: `npm test`

## File Naming
- Test files: `{ComponentName}.test.tsx` or `{module}.test.ts`
- Co-located with source files

## Patterns
- Use `describe` / `it` blocks with descriptive names
- Arrange / Act / Assert pattern
- Render components with `render()` from React Testing Library
- Query elements with `screen.getByRole()`, `screen.getByText()` — prefer accessible queries
- User interactions with `userEvent` (not `fireEvent`)

## Mocking
- Use `vi.mock()` for module mocks
- Use `vi.fn()` for function mocks
- Mock API calls at the API layer level, not at fetch/axios level
- Reset mocks in `beforeEach`

## Rules
- Each test tests one behavior
- Tests should be independent — no shared mutable state
- Prefer factories for test data over hardcoded objects
- Always test loading, success, and error states for async operations
