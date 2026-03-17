---
description: "Use when writing or modifying TypeScript and React files. Covers naming conventions, component patterns, type practices, and import organization for this project."
applyTo: "**/*.ts,**/*.tsx"
---
# TypeScript & React Conventions

## Naming
- Variables and functions: camelCase
- Components, types, interfaces: PascalCase
- Constants: UPPER_SNAKE_CASE for true constants
- Files: PascalCase for components (`UserProfile.tsx`), camelCase for utilities (`formatDate.ts`)

## Components
- Functional components only, no class components
- Default export per file: `export default function UserProfile() {}`
- Props defined with interface: `interface UserProfileProps { userId: string }`
- Destructure props in function signature

## Types
- Prefer `interface` for component props and API response shapes
- Use `type` for unions, intersections, and utility types
- Avoid `any` — use `unknown` with type guards
- Co-locate types with their feature, shared types in `src/shared/types/`

## Imports
- React imports first
- Third-party imports second
- Absolute imports (`@/...`) third
- Relative imports last
- Separate groups with blank lines

## Error Handling
- Use Error Boundaries for component trees
- API errors handled in the shared API layer
- Display user-friendly error messages, log details to console
