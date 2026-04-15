---
description: "Use when writing files inside the app/ directory or features/ directory. Covers Next.js App Router conventions: Server Components, Client Components, Server Actions, layouts, and data fetching patterns."
applyTo: "{app,features}/**/*.{ts,tsx}"
---
## Server vs Client Components

**Server Component** (default — no directive needed):
- `async` functions that fetch data directly
- No `useState`, `useEffect`, or event handlers
- Can import from `server-only` modules
- Rendered on the server; not part of the client bundle

**Client Component** (`"use client"` at top of file):
- Requires interactivity: `onClick`, `onChange`, form control
- Requires browser APIs: `localStorage`, `window`, `navigator`
- Requires React state: `useState`, `useReducer`, `useContext`
- Minimize size — pass data as props from server parents

**Decision rule**: Start with Server Component. Add `"use client"` only when you hit a specific requirement above.

## Data Fetching (Server Components)

```typescript
// features/users/queries.ts
import 'server-only'

export async function getUser(id: string) {
  // Direct DB or fetch call — runs on server only
}

// app/(dashboard)/users/[id]/page.tsx
import { getUser } from '@/features/users/queries'

export default async function UserPage({ params }: { params: Promise<{ id: string }> }) {
  const { id } = await params
  const user = await getUser(id)
  return <UserDetail user={user} />
}
```

## Server Actions (Mutations)

```typescript
// features/users/actions.ts
'use server'

import { z } from 'zod'
import { auth } from '@/lib/auth'

const schema = z.object({ name: z.string().min(1) })

export async function updateUser(formData: FormData) {
  const session = await auth()
  if (!session) throw new Error('Unauthorized')

  const parsed = schema.safeParse(Object.fromEntries(formData))
  if (!parsed.success) return { success: false, error: parsed.error.flatten() }

  // mutation logic
  return { success: true }
}
```

## Route Segments

- `loading.tsx` — mandatory for any page with async data fetching
- `error.tsx` — mandatory for any page that can throw; must be `"use client"`
- `not-found.tsx` — for 404 states
- `layout.tsx` — shared UI that persists across child routes

## Patterns to Avoid
- `useEffect` for data fetching in client components — use server queries instead
- Direct DB calls in `app/` pages — delegate to `features/{name}/queries.ts`
- Passing full DB records as props to client components — serialize only needed fields
