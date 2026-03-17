# Skill Templates

Templates for generating `SKILL.md` files with folder structure, scripts, and references.

## Folder Structure

Every skill follows this layout:

```
.github/skills/{skill-name}/
├── SKILL.md                    # Required — name must match folder
├── scripts/                    # Executable scripts (optional)
│   └── {script}.sh
└── references/                 # Reference docs loaded on demand (optional)
    └── {topic}.md
```

## Run Tests Skill

```
.github/skills/run-tests/
├── SKILL.md
└── scripts/
    └── run-tests.sh
```

### SKILL.md

```markdown
---
name: run-tests
description: "Run the project test suite and report results. Use when executing tests, checking test coverage, verifying changes pass tests, or debugging test failures."
---

# Run Tests

## When to Use
- After making code changes to verify nothing is broken
- When writing new tests and need to run them
- When debugging test failures
- When checking test coverage

## Procedure
1. Identify the test framework and runner from project config
2. Run the test suite using [run-tests script](./scripts/run-tests.sh)
3. If tests fail, analyze the failure output:
   - Identify which tests failed and why
   - Check if failure is in new code or existing tests
   - Suggest fixes for failing tests
4. Report results with: pass/fail count, failed test names, and coverage if available
```

### scripts/run-tests.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

# Detect and run the appropriate test command
# This script is customized per-repo during ADAS generation

{test_command}
```

Replace `{test_command}` with the actual detected test command (e.g., `npm test`, `pytest`, `go test ./...`, `cargo test`).

## Lint and Format Skill

```
.github/skills/lint-format/
├── SKILL.md
└── scripts/
    └── lint.sh
```

### SKILL.md

```markdown
---
name: lint-format
description: "Run linters and formatters on the project. Use when checking code quality, fixing lint errors, formatting code, or verifying style compliance."
---

# Lint and Format

## When to Use
- Before committing code to check for lint errors
- After making changes to ensure code style compliance
- When fixing lint warnings or formatting issues

## Procedure
1. Run the linter using [lint script](./scripts/lint.sh)
2. If there are fixable errors, run with auto-fix flag
3. Report results: errors found, auto-fixed count, remaining manual fixes needed
```

## Deploy Skill

```
.github/skills/deploy/
├── SKILL.md
└── references/
    └── environments.md
```

### SKILL.md

```markdown
---
name: deploy
description: "Deploy the application to target environments. Use when deploying, checking deployment status, updating deployment configs, or rolling back deployments."
---

# Deployment

## When to Use
- Deploying to staging or production
- Checking deployment configuration
- Updating CI/CD pipeline
- Rolling back a deployment

## Procedure
1. Verify the current deployment configuration in [environments reference](./references/environments.md)
2. Ensure all tests pass before deploying
3. Follow the deployment steps for the target environment
4. Verify the deployment succeeded
```

## Database Migrations Skill

```
.github/skills/db-migrations/
├── SKILL.md
└── references/
    └── migration-patterns.md
```

### SKILL.md

```markdown
---
name: db-migrations
description: "Create and run database migrations. Use when modifying database schemas, creating new tables, adding columns, or managing data transformations."
---

# Database Migrations

## When to Use
- Creating new database tables or modifying schemas
- Adding or removing columns
- Creating data migrations or transformations
- Rolling back problematic migrations

## Procedure
1. Review existing migration files for patterns and conventions
2. Create a new migration following the project's naming convention
3. Write both up and down (rollback) migrations
4. Test the migration in development before applying to staging
5. Verify the migration with a rollback test

## Rules
- Always create reversible migrations
- Never drop columns in the same release as code removal
- Test rollback before merging
- Use transactions for data migrations when supported
```

## Customization Notes

When generating skills for a target repo:

1. **`name` must match folder**: `run-tests` skill lives in `run-tests/` folder
2. **Replace placeholders**: Fill in `{test_command}`, `{lint_command}`, etc. with actual detected commands
3. **Keep SKILL.md < 500 lines**: Use `references/` for detailed docs
4. **Make scripts executable**: Generated `.sh` scripts need `chmod +x`
5. **Skip irrelevant skills**: Don't generate `deploy` if no deployment config detected, don't generate `db-migrations` if no database detected
6. **Description keywords**: Include action words that agents will use in context (e.g., "test", "lint", "deploy", "migrate")
