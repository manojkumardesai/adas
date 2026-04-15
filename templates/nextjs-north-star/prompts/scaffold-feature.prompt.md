---
description: "Scaffold a new feature following North Star Architecture. Creates the full feature directory with components, actions, queries, types, and index barrel."
agent: implementer
argument-hint: "Feature name and brief description of what it does"
---
Scaffold a new feature in `features/` following North Star Architecture.

Feature to create: {{feature-name}}

Create the following structure:
```
features/{{feature-name}}/
  components/
    {{FeatureName}}View.tsx    # Server component — main display
  actions/
    actions.ts                 # Server Actions with "use server"
  queries/
    queries.ts                 # Data fetching with 'server-only'
  types.ts                     # Feature-specific TypeScript types
  index.ts                     # Public API — barrel export
```

For each file:
- `View.tsx`: async Server Component that calls the query function and renders UI
- `actions.ts`: at minimum a create and update action, with Zod validation and auth check
- `queries.ts`: `getAll` and `getById` functions, import 'server-only'
- `types.ts`: TypeScript types matching the data shape
- `index.ts`: export only public-facing components and types

Then create a thin page at `app/(dashboard)/{{feature-name}}/page.tsx` that imports from the feature.
