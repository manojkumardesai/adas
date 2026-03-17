# Project Guidelines

## Overview
A React single-page application built with TypeScript, Vite, and React Router.

## Tech Stack
- **Language**: TypeScript
- **Framework**: React 18 with React Router v6
- **Build Tool**: Vite
- **Package Manager**: npm
- **Key Dependencies**: React, React Router, Axios, Zustand, Tailwind CSS

## Architecture
Feature-based organization under `src/features/`. Each feature contains its own components, hooks, and API layer. Shared utilities and components live in `src/shared/`.

## Code Style
- Follow existing patterns — read nearby files before writing new code
- Naming: camelCase for variables/functions, PascalCase for components/types
- ESLint: configured in `eslint.config.js`. Run `npm run lint` to check.
- Prettier: configured in `.prettierrc`. Run `npm run format` to format.

## Build and Test
- **Install**: `npm install`
- **Dev**: `npm run dev`
- **Build**: `npm run build`
- **Test**: `npm test` (Vitest)
- **Lint**: `npm run lint`

Always run tests after making changes. Fix failures before moving on.

## Conventions
- Components are default-exported, one per file
- Hooks are prefixed with `use` and co-located with features
- API calls go through the `src/shared/api/` layer, never directly in components
- Use Zustand stores for state shared across features
- Tailwind for styling — no CSS modules or styled-components
