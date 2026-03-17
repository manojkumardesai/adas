---
description: "Generate comprehensive Vitest tests for React components or TypeScript modules"
agent: tester
argument-hint: "What to test (e.g., 'the UserProfile component' or 'the auth utility functions')"
---
Generate comprehensive tests for the specified code:

1. Read existing test files to understand the project's Vitest and React Testing Library patterns
2. Read the source code to identify the public API and key behaviors
3. Generate tests covering:
   - Rendering and display states (loading, success, error, empty)
   - User interactions (clicks, form submissions, navigation)
   - Edge cases (empty data, long strings, missing props)
   - Async operations (API calls, state updates)
4. Follow the project's existing test patterns — same assertion style, same query preferences, same mock approach
5. Run the tests to verify they pass
