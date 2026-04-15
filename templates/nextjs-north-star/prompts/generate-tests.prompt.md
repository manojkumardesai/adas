---
description: "Generate tests for a Next.js feature — Server Actions, query functions, and React components."
agent: tester
argument-hint: "Feature or file path to generate tests for"
---
Generate comprehensive tests for the specified feature or file.

Target: {{feature-or-file}}

Cover:
1. **Server Actions** — authenticated, unauthenticated, validation errors, success path
2. **Query functions** — found, not found, filtered results
3. **Client Components** — render, user interactions, form submissions
4. **Server Components** — test via their underlying query/action functions (not render)

Follow existing test patterns. Run `pnpm test` when done and report results.
