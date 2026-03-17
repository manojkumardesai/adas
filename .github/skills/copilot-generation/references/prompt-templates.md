# Prompt Templates

Templates for generating `.prompt.md` files. Each prompt is a single focused task routed to the appropriate agent.

## Generate Tests

```markdown
---
description: "Generate comprehensive tests for selected code or a specific module"
agent: tester
argument-hint: "Describe what to test (e.g., 'the user authentication module')"
---
Generate comprehensive tests for the specified code:

1. Read existing test files to understand the project's test patterns and conventions
2. Identify the code to test and its public API / key behaviors
3. Generate tests covering:
   - Happy path scenarios
   - Edge cases (empty input, boundary values, null/undefined)
   - Error scenarios (invalid input, network failures, permission errors)
4. Follow the project's existing test patterns:
   - Same framework and assertion style
   - Same file naming convention
   - Same test helper and fixture usage
5. Run the tests to verify they pass
```

## Scaffold Component

```markdown
---
description: "Scaffold a new UI component following project conventions"
agent: implementer
argument-hint: "Component name and purpose (e.g., 'UserProfile card that displays avatar, name, and role')"
---
Scaffold a new component following the project's conventions:

1. Read 2-3 existing components to understand the pattern:
   - File structure and naming
   - Props/interface pattern
   - Styling approach
   - Export pattern
2. Create the component file(s) following the same patterns
3. Include:
   - TypeScript types/interfaces for props
   - Default/loading/error states if applicable
   - Basic styling following project conventions
4. Create a test file for the component
5. Add export to the appropriate index/barrel file if one exists
```

## Create API Endpoint

```markdown
---
description: "Create a new API endpoint or route handler following project conventions"
agent: implementer
argument-hint: "Endpoint description (e.g., 'GET /api/users/:id - fetch user by ID')"
---
Create a new API endpoint following the project's conventions:

1. Read existing route handlers to understand the pattern:
   - File location and naming
   - Request validation approach
   - Response format
   - Error handling
   - Authentication/authorization middleware
2. Create the endpoint with:
   - Input validation at the handler boundary
   - Proper error handling and status codes
   - Response in the project's standard format
   - Auth middleware if required
3. Create tests for the endpoint covering:
   - Success case
   - Validation errors
   - Not found / unauthorized cases
4. Register the route in the router configuration
```

## Review Code

```markdown
---
description: "Review recent changes or a specific file for code quality, correctness, and security"
agent: reviewer
argument-hint: "What to review (e.g., 'the auth middleware changes' or 'src/api/users.ts')"
---
Review the specified code for quality, correctness, and security:

1. Read the code and understand its purpose
2. Check for:
   - **Correctness**: Logic errors, missing edge cases, off-by-one errors
   - **Security**: Injection vulnerabilities, auth bypass, data exposure, OWASP Top 10
   - **Conventions**: Adherence to project patterns and naming conventions
   - **Testing**: Adequate test coverage for the changes
   - **Performance**: Obvious performance issues (N+1 queries, unnecessary re-renders)
3. Provide structured feedback:
   - Critical issues that must be fixed
   - Warnings that should be addressed
   - Suggestions for improvement
```

## Generate Documentation

```markdown
---
description: "Generate or update documentation for a module, API, or the project"
agent: docs
argument-hint: "What to document (e.g., 'the authentication system' or 'update the README')"
---
Generate documentation for the specified topic:

1. Read the source code to understand the implementation
2. Read existing docs to understand the style and structure
3. Generate documentation that includes:
   - Overview and purpose
   - Usage examples with code snippets
   - API reference if applicable
   - Common patterns and gotchas
4. Use the project's documentation style:
   - Same markdown structure and heading levels
   - Same level of detail
   - Same tone (formal/informal)
5. Link to source files rather than copying large code blocks
```

## Create Implementation Plan

```markdown
---
description: "Create a detailed implementation plan for a feature or change"
agent: planner
argument-hint: "Describe the feature or change to plan"
---
Create a detailed implementation plan:

1. Understand the requirement fully — ask clarifying questions if needed
2. Explore the codebase to understand:
   - Existing relevant code and patterns
   - Architecture and dependencies
   - Test patterns and coverage
3. Produce a step-by-step plan with:
   - Files to create or modify (with full paths)
   - Key changes per file (what to add/modify/remove)
   - Dependency ordering (what must happen first)
   - Test strategy (what tests to add)
   - Edge cases and risks
   - Estimated complexity (small/medium/large)
```

## Customization Notes

1. **Route to correct agent**: Set `agent:` to match the generated agent's filename (minus `.agent.md`)
2. **Only generate relevant prompts**: Don't create `scaffold-component` if no UI framework detected
3. **Argument hints**: Provide a concrete example in the hint text
4. **Single task focus**: Each prompt does ONE thing. Don't combine "create and test and deploy"
5. **Reference project conventions**: Prompts should tell the agent to read existing code for patterns, not prescribe patterns
