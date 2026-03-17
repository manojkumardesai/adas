---
description: "Scaffold a new React component following project conventions"
agent: implementer
argument-hint: "Component name and purpose (e.g., 'UserAvatar that displays user profile image with fallback')"
---
Scaffold a new React component following the project's feature-based architecture:

1. Read 2-3 existing components to understand the pattern:
   - File structure (single file or directory with index)
   - Props interface pattern
   - Styling approach (Tailwind classes)
   - Export pattern (default export)
2. Create the component in the appropriate feature directory under `src/features/`
3. Include:
   - TypeScript interface for props
   - Tailwind styling
   - Loading/error states where applicable
4. Create a co-located test file using Vitest + React Testing Library
5. Run tests to verify the component renders correctly
