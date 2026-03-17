# Project Guidelines

## Overview
A Python REST API built with FastAPI, SQLAlchemy, and Alembic for database migrations.

## Tech Stack
- **Language**: Python 3.12
- **Framework**: FastAPI
- **ORM**: SQLAlchemy 2.0 with async support
- **Migrations**: Alembic
- **Package Manager**: uv (with pyproject.toml)
- **Key Dependencies**: FastAPI, SQLAlchemy, Pydantic, Alembic, pytest

## Architecture
Layered architecture: `app/api/` (routes) → `app/services/` (business logic) → `app/models/` (SQLAlchemy models). Pydantic schemas in `app/schemas/`. Config in `app/core/`.

## Code Style
- Follow existing patterns — read nearby files before writing new code
- Naming: snake_case for variables/functions, PascalCase for classes
- Ruff: configured in `pyproject.toml`. Run `ruff check .` and `ruff format .`
- Type hints on all function signatures

## Build and Test
- **Install**: `uv sync`
- **Run**: `uv run uvicorn app.main:app --reload`
- **Test**: `uv run pytest`
- **Lint**: `uv run ruff check .`
- **Format**: `uv run ruff format .`
- **Migrate**: `uv run alembic upgrade head`

Always run tests after making changes. Fix failures before moving on.

## Conventions
- Route handlers are thin — delegate to service layer
- All database access goes through the service layer, never directly in routes
- Use Pydantic models for request/response validation
- Async everywhere — all route handlers and DB operations are async
- Use dependency injection for DB sessions and auth
