---
description: "Use when working in the features/ directory or creating new features. Covers North Star Architecture rules: feature colocation, public APIs, cross-feature boundaries, and the shared layer."
applyTo: "features/**"
---
## North Star Architecture Rules

### Feature Structure
Each feature lives in `features/{feature-name}/` and owns its full vertical slice:

```
features/
  user-profile/
    components/         # React components (server and client)
      UserCard.tsx      # Server component — no directive
      EditForm.tsx      # "use client" — form with state
    hooks/              # Client-side hooks only
      use-user-form.ts
    actions/            # Server Actions
      actions.ts        # "use server" at top
    queries/            # Data fetching (server-only)
      queries.ts        # import 'server-only'
    types.ts            # Feature-specific types
    index.ts            # Public API — only export what other features need
```

### Cross-Feature Rules
- Features communicate ONLY through their `index.ts` public API
- Never import directly from another feature's internal files: `features/orders/components/Item.tsx` ❌
- Use barrel export: `import { OrderSummary } from '@/features/orders'` ✓
- Shared utilities go in `lib/` or `shared/` — not in feature directories

### App Layer (Thin Pages)
`app/` pages should be thin orchestrators:

```typescript
// ✓ Correct — thin page
import { UserProfile } from '@/features/user-profile'

export default async function ProfilePage({ params }) {
  return <UserProfile userId={(await params).id} />
}

// ✗ Wrong — logic in the page
export default async function ProfilePage({ params }) {
  const user = await db.user.findUnique(...)  // belongs in queries.ts
  return <div>{user.name}</div>
}
```

### Shared Layer
`shared/` contains components and hooks used by 2+ features:
- Only promote code to `shared/` when it's actually shared
- Don't pre-emptively abstract — wait for the second use case
- `lib/` is for non-component utilities (db client, auth helpers, formatting)
