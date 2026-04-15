---
description: "Review code changes for North Star Architecture compliance, RSC correctness, and security."
agent: reviewer
argument-hint: "Files or feature to review"
---
Review the specified files or recent changes for:

1. **North Star Architecture compliance** — feature colocation, thin pages, no cross-feature internal imports
2. **RSC correctness** — correct use of server vs client components, no data fetching in client components
3. **Server Action security** — input validation, auth checks, no data exposure
4. **General code quality** — TypeScript correctness, error handling, missing loading/error states

Target: {{files-or-feature}}

Provide a structured review with critical issues, warnings, and suggestions.
