---
description: "Use when writing or modifying Python files. Covers naming conventions, type hints, import organization, async patterns, and error handling for this FastAPI project."
applyTo: "**/*.py"
---
# Python Conventions

## Naming
- Variables and functions: snake_case
- Classes: PascalCase
- Constants: UPPER_SNAKE_CASE
- Private: prefix with single underscore
- Files: snake_case

## Type Hints
- Required on all function signatures (params and return type)
- Use `from __future__ import annotations` for forward references
- Prefer `str | None` over `Optional[str]`
- Use Pydantic models for complex types

## Imports
- Standard library first
- Third-party second
- Local imports third
- Separated by blank lines
- Use absolute imports from `app.` package

## Async Patterns
- All route handlers are `async def`
- All database operations use async sessions
- Use `asyncio.gather()` for concurrent independent operations
- Never use sync blocking calls in async handlers

## Error Handling
- Use specific exception types: `HTTPException`, `ValueError`, custom exceptions
- Never bare `except:` — always specify the exception type
- Business logic errors raise domain exceptions, route handlers translate to HTTP errors
- Log errors with structured logging
