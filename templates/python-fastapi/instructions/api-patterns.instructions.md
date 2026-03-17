---
description: "Use when writing or modifying API route handlers in app/api/. Covers route patterns, request validation, response format, dependency injection, and error handling."
applyTo: "app/api/**/*.py"
---
# API Route Conventions

## Route Organization
- Routes in `app/api/routes/` grouped by resource (e.g., `users.py`, `items.py`)
- Router instance per file: `router = APIRouter(prefix="/users", tags=["users"])`
- Registered in `app/api/__init__.py`

## Request Handling
- Use Pydantic schemas for request body validation (from `app/schemas/`)
- Path params typed in function signature
- Query params with default values and type annotations
- Use `Depends()` for DB sessions, auth, and shared dependencies

## Response Format
- Return Pydantic response models (defined in `app/schemas/`)
- Use `response_model=` parameter on route decorator
- Status codes: 200 (success), 201 (created), 204 (no content), 404 (not found), 422 (validation error)

## Error Handling
- Raise `HTTPException` for client errors
- Let Pydantic handle validation errors (automatic 422)
- Service exceptions translated to HTTP errors in route handlers
- Never expose internal error details to clients

## Authentication
- Use `Depends(get_current_user)` for protected routes
- Current user injected via dependency injection
