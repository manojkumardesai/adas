# Project Context

This is a **Next.js** application using the **App Router** with **North Star Architecture** — a feature-first, server-component-default approach where business logic, UI, and data access are colocated per feature.

## Tech Stack
- **Framework**: Next.js 14+ (App Router)
- **Language**: TypeScript
- **Styling**: Tailwind CSS
- **State**: React Server Components for async state, Zustand for client-side global state
- **Testing**: Vitest + React Testing Library + Playwright (E2E)
- **Package Manager**: pnpm

## Architecture: North Star

```
app/
  (auth)/              # Route group — auth pages
  (dashboard)/         # Route group — dashboard pages
    layout.tsx         # Shared layout (server component)
    page.tsx           # Thin entry point — imports from features/
features/
  {feature-name}/
    components/        # Client and server components
    hooks/             # Client hooks only
    actions/           # Server Actions ("use server")
    queries/           # Data fetching functions (server-only)
    types.ts           # Feature types
    index.ts           # Public API (barrel export)
lib/
  db.ts                # Database client
  auth.ts              # Auth helpers
  utils.ts             # Shared utilities
shared/
  components/          # Shared UI components
  hooks/               # Shared client hooks
```

**Key principles:**
- Server Components are the default — only add `"use client"` when needed (interactivity, browser APIs, client state)
- Data fetching lives in `features/{feature}/queries/` using async server components
- Mutations use Server Actions in `features/{feature}/actions/`
- `app/` pages are thin — they import and compose from `features/`
- No direct database calls in `app/` pages or components

## Commands
- **Install**: `pnpm install`
- **Dev**: `pnpm dev`
- **Build**: `pnpm build`
- **Test (unit)**: `pnpm test`
- **Test (E2E)**: `pnpm test:e2e`
- **Lint**: `pnpm lint`
- **Type check**: `pnpm typecheck`

## Conventions
- File naming: `kebab-case` for files and directories
- Component naming: `PascalCase`
- Server Actions: files named `actions.ts` with `"use server"` at top
- Queries: files named `queries.ts`, server-only, can use `import 'server-only'`
- See [CONTRIBUTING.md](CONTRIBUTING.md) for full guidelines
