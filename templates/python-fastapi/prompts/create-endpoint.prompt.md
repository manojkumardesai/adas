---
description: "Create a new FastAPI endpoint with Pydantic schemas, service layer, and tests"
agent: implementer
argument-hint: "Endpoint description (e.g., 'POST /api/users - create a new user with email and password')"
---
Create a new API endpoint following the project's layered architecture:

1. Read existing route handlers in `app/api/routes/` to understand the pattern
2. Create or update the Pydantic schemas in `app/schemas/`
3. Create the service function in `app/services/`
4. Create the route handler in `app/api/routes/`
5. Register the route in the router configuration
6. Create an Alembic migration if new database models are needed
7. Create tests covering success, validation errors, and edge cases
8. Run `uv run pytest` to verify all tests pass
