# Instruction Templates

Templates for generating `.instructions.md` files with appropriate `applyTo` patterns and keyword-rich descriptions.

## Project Context (always-on)

Generate this for **every** repo. No `applyTo` → always loaded. It points every agent at the persisted scan so they share the same grounding.

```markdown
---
description: "Always-on project grounding. Read the repo context before planning, editing, reviewing, or testing — it holds the stack, commands, conventions, architecture, and capabilities for this repo."
---
# Project Context

Before doing anything, read [`.github/adas/context.md`](./adas/context.md). It is the source of truth for:
- Tech stack and exact build/test/lint/run commands
- Naming and architecture conventions (follow them; do not invent new ones)
- Architecture and key flows (diagrams)
- Detected capabilities

Do not duplicate that content here — read it. If a command or convention is missing from `context.md`, ask rather than guess.

## Guardrail (all agents)
- Work ends at a dirty working tree. **Never** run `git commit`, `git push`, or any history-writing git command — commits are human-gated.
{multi-repo only:}
- Stay within this repo's directory. Cross-repo work is coordinated by the workspace coordinator.
```

## Language Conventions

### TypeScript

```markdown
---
description: "Use when writing or modifying TypeScript files. Covers naming conventions, type patterns, import organization, and error handling for this project."
applyTo: "**/*.ts,**/*.tsx"
---
# TypeScript Conventions

## Naming
- Variables and functions: camelCase
- Types and interfaces: PascalCase (prefix interfaces with `I` only if project convention)
- Constants: UPPER_SNAKE_CASE for true constants, camelCase for computed values
- Files: {detected pattern — kebab-case or camelCase}

## Types
- Prefer `interface` for object shapes, `type` for unions/intersections
- Avoid `any` — use `unknown` with type guards
- Use strict null checks — handle `null` and `undefined` explicitly

## Imports
- {detected import order pattern}
- Use path aliases if configured: {detected aliases}

## Error Handling
- Use typed errors at service boundaries
- {detected error handling pattern}
```

### Python

```markdown
---
description: "Use when writing or modifying Python files. Covers naming conventions, type hints, import organization, and error handling for this project."
applyTo: "**/*.py"
---
# Python Conventions

## Naming
- Variables and functions: snake_case
- Classes: PascalCase
- Constants: UPPER_SNAKE_CASE
- Private: prefix with single underscore

## Type Hints
- Use type hints for function signatures
- Use `from __future__ import annotations` for forward references
- Prefer `str | None` over `Optional[str]` (Python 3.10+)

## Imports
- Standard library, third-party, local — separated by blank lines
- {detected import organization}

## Error Handling
- Use specific exception types, not bare `except`
- {detected error handling pattern}
```

### Go

```markdown
---
description: "Use when writing or modifying Go files. Covers naming conventions, error handling, package organization, and testing patterns for this project."
applyTo: "**/*.go"
---
# Go Conventions

## Naming
- Exported: PascalCase, unexported: camelCase
- Interfaces: single method → -er suffix (Reader, Writer)
- Package names: short, lowercase, no underscores

## Error Handling
- Always check returned errors — never use `_` to ignore errors
- Return errors, don't panic
- Wrap errors with context: `fmt.Errorf("operation failed: %w", err)`

## Package Organization
- {detected package structure}
```

## Testing

```markdown
---
description: "Use when writing or modifying test files. Covers test organization, assertion patterns, mocking conventions, and test naming for this project."
applyTo: "{test file pattern}"
---
# Testing Conventions

## Test Framework
- Framework: {detected framework}
- Runner: {detected runner command}

## Naming
- Test files: {detected pattern — *.test.ts, *_test.go, test_*.py}
- Test functions: {detected pattern — describe/it, Test*, test_*}
- Use descriptive names that explain the scenario

## Patterns
- {Arrange/Act/Assert or Given/When/Then}
- {detected mocking approach}
- {detected fixture/factory patterns}

## Rules
- Each test tests one behavior
- Tests should be independent — no shared mutable state
- Use factories/fixtures for test data, not hardcoded values
- Always test edge cases and error paths
```

## API Patterns

```markdown
---
description: "Use when writing or modifying API route handlers, controllers, or endpoint files. Covers request validation, response format, error handling, and authentication patterns."
applyTo: "{api directory pattern}"
---
# API Conventions

## Route Organization
- {detected routing pattern}

## Request Handling
- Validate all input at the handler boundary
- {detected validation approach}

## Response Format
- {detected response format — JSON:API, custom envelope, plain JSON}
- {detected error response format}

## Authentication
- {detected auth middleware pattern}

## Error Handling
- Use consistent error response format
- Return appropriate HTTP status codes
- {detected error handling pattern}
```

## Component Patterns (React/Vue/Svelte)

```markdown
---
description: "Use when writing or modifying UI components. Covers component structure, props, state management, and styling patterns for this project."
applyTo: "{component file pattern}"
---
# Component Conventions

## Structure
- {functional vs class components}
- {detected file organization — component per file, co-located styles}

## Props
- {detected prop definition pattern — interfaces, PropTypes}
- Use destructuring in function signatures

## State Management
- {detected state management — useState, Redux, Zustand, Pinia, etc.}

## Styling
- {detected styling approach — CSS modules, Tailwind, styled-components, etc.}

## Naming
- Components: PascalCase
- Files: {detected pattern}
- Event handlers: handle{Event} or on{Event}
```

## Security

```markdown
---
description: "Use when writing code that handles authentication, authorization, user input, database queries, or external API calls. Covers security best practices and OWASP Top 10 prevention."
---
# Security Guidelines

## Input Validation
- Validate and sanitize all user input at system boundaries
- Use parameterized queries for database operations — never string concatenation
- Validate file uploads: type, size, and content

## Authentication & Authorization
- {detected auth patterns}
- Check authorization on every protected endpoint
- Use least-privilege principle for service accounts

## Data Protection
- Never log sensitive data (passwords, tokens, PII)
- Use environment variables for secrets — never commit secrets to code
- {detected secrets management approach}

## Common Vulnerabilities
- Prevent XSS: escape output in templates, use framework defaults
- Prevent CSRF: use framework tokens
- Prevent injection: parameterized queries, input validation
- Set security headers: CORS, CSP, HSTS
```

## Customization Notes

1. **Replace placeholders**: Fill `{detected pattern}` with actual values from the scan report
2. **applyTo precision**: Use specific globs, not `"**"`. Match actual file extensions and directories.
3. **One concern per file**: Create separate files for testing, API, components, security — never combine them
4. **Description keywords**: Start with "Use when writing or modifying..." to enable on-demand discovery
5. **Show, don't tell**: Include brief code snippets showing the correct pattern rather than lengthy explanations
6. **Skip irrelevant files**: Don't generate API instructions if no API layer detected, don't generate component instructions if no UI framework
