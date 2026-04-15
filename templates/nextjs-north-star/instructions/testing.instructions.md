---
description: "Use when writing test files for Next.js components, Server Actions, and query functions. Covers Vitest + React Testing Library patterns, mocking server modules, and testing async server components."
applyTo: "**/*.{test,spec}.{ts,tsx}"
---
## Test Stack
- **Unit/Component**: Vitest + React Testing Library
- **E2E**: Playwright

## File Naming
- Unit tests: colocated with source — `{name}.test.ts` or `{name}.test.tsx`
- E2E tests: `e2e/{feature}.spec.ts`

## Component Tests (Client Components)
```typescript
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'
import { EditForm } from './EditForm'

it('submits the form with valid data', async () => {
  const user = userEvent.setup()
  const mockAction = vi.fn()
  render(<EditForm action={mockAction} />)

  await user.type(screen.getByRole('textbox', { name: /name/i }), 'Alice')
  await user.click(screen.getByRole('button', { name: /save/i }))

  expect(mockAction).toHaveBeenCalled()
})
```

## Server Action Tests
```typescript
import { updateUser } from './actions'
import { vi } from 'vitest'

// Mock auth and db
vi.mock('@/lib/auth', () => ({ auth: vi.fn() }))
vi.mock('@/lib/db', () => ({ db: { user: { update: vi.fn() } } }))

it('returns error when not authenticated', async () => {
  vi.mocked(auth).mockResolvedValue(null)
  const formData = new FormData()
  const result = await updateUser(formData)
  expect(result.success).toBe(false)
})
```

## Query Function Tests
```typescript
import { getUser } from './queries'
vi.mock('@/lib/db')

it('returns user by id', async () => {
  vi.mocked(db.user.findUnique).mockResolvedValue({ id: '1', name: 'Alice' })
  const result = await getUser('1')
  expect(result?.name).toBe('Alice')
})
```

## Mocking Next.js Internals
```typescript
vi.mock('next/navigation', () => ({
  useRouter: () => ({ push: vi.fn(), refresh: vi.fn() }),
  usePathname: () => '/test',
}))
vi.mock('next/headers', () => ({
  cookies: () => ({ get: vi.fn() }),
}))
```

## Rules
- Use `getByRole` and `getByLabelText` over `getByTestId`
- Use `userEvent` over `fireEvent`
- Never test implementation details — test behavior from the user's perspective
- Mock at the module boundary, not inside components
