---
description: "Use when writing TypeScript or TSX files in this Next.js project. Covers naming conventions, import organization, type patterns, and general TypeScript rules."
applyTo: "**/*.{ts,tsx}"
---
## TypeScript Conventions

- **Files**: `kebab-case` (e.g., `user-profile.tsx`, `use-auth.ts`)
- **Components**: `PascalCase` named exports (e.g., `export function UserProfile()`)
- **Hooks**: `camelCase` prefixed with `use` (e.g., `useUserProfile`)
- **Types/Interfaces**: `PascalCase` (e.g., `type UserProfile = ...`)
- **Constants**: `SCREAMING_SNAKE_CASE` for module-level, `camelCase` for local

## Import Order
1. React and Next.js built-ins
2. Third-party packages
3. Internal aliases (`@/features/`, `@/shared/`, `@/lib/`)
4. Relative imports

## Type Safety
- Prefer `type` over `interface` for object shapes
- Use `satisfies` operator for config objects with known shapes
- Never use `any` — use `unknown` and narrow
- Mark async server component props as `Promise<>` when using `params`/`searchParams` in Next.js 15

## Error Handling
- Use `Result` pattern or typed error unions for Server Actions
- Never throw plain strings — throw `Error` instances with descriptive messages
- Return `{ success: true, data }` or `{ success: false, error }` from Server Actions
