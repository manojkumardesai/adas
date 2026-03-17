---
description: "Use when writing or modifying test files. Covers pytest patterns, fixtures, async testing, and mocking conventions for this FastAPI project."
applyTo: "tests/**/*.py"
---
# Testing Conventions

## Framework
- Test runner: pytest with pytest-asyncio
- Run tests: `uv run pytest`
- Run specific: `uv run pytest tests/test_users.py -v`

## File Naming
- Test files: `test_{module}.py` in `tests/` directory
- Test functions: `test_{behavior_description}`
- Fixtures in `conftest.py` at each test directory level

## Patterns
- Use `@pytest.mark.asyncio` for async tests
- Arrange / Act / Assert pattern
- Use FastAPI `TestClient` or `httpx.AsyncClient` for endpoint tests
- Use factory fixtures for test data

## Fixtures
- DB session fixture in `tests/conftest.py`
- Use transaction rollback per test for isolation
- Factory functions for creating test entities

## Rules
- Each test tests one behavior
- Tests should be independent — no shared mutable state
- Mock external services, not internal ones
- Test both success and error paths for every endpoint
