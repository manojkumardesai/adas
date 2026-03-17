---
description: "Generate comprehensive pytest tests for FastAPI endpoints or Python modules"
agent: tester
argument-hint: "What to test (e.g., 'the user registration endpoint' or 'the order service')"
---
Generate comprehensive tests for the specified code:

1. Read existing tests and conftest.py to understand the project's pytest patterns
2. Read the source code to identify endpoints, service functions, and key behaviors
3. Generate tests covering:
   - Success cases with valid input
   - Validation errors (missing fields, invalid types)
   - Not found cases (invalid IDs)
   - Authorization errors (missing/invalid token)
   - Edge cases (empty lists, duplicate entries)
4. Use existing fixtures and factory patterns
5. Run the tests to verify they pass
