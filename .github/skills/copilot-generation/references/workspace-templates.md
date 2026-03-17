# Workspace Instruction Templates

Templates for generating `copilot-instructions.md` — the foundation file that all agents in the target repo inherit.

## Standard Template

```markdown
# Project Guidelines

## Overview
{Brief project description — what it does, who it's for}

## Tech Stack
- **Language**: {primary language}
- **Framework**: {primary framework}
- **Package Manager**: {package manager}
- **Key Dependencies**: {top 5 most important}

## Architecture
{Brief architecture description — major components, service boundaries, data flow}
{Link to architecture docs if they exist: See [architecture docs](docs/architecture.md)}

## Code Style
- Follow existing patterns in the codebase — read nearby files before writing new code
- Naming: {detected naming convention}
- {Linter}: configured in {config path}. Run `{lint command}` to check.
- {Formatter}: configured in {config path}. Run `{format command}` to format.

## Build and Test
- **Install**: `{install command}`
- **Build**: `{build command}`
- **Test**: `{test command}`
- **Lint**: `{lint command}`

Always run tests after making changes. Fix failures before moving on.

## Conventions
{Only patterns that differ from common practices — things agents would get wrong without being told}

## Documentation
{Links to existing docs rather than duplicating content}
- README: [README.md](README.md)
- {Other docs if they exist}
```

## Minimal Template (small repos)

For small repos (< 50 source files) that don't need extensive instructions:

```markdown
# Project Guidelines

## Stack
{Language} + {Framework}. Install: `{install}`. Test: `{test}`. Lint: `{lint}`.

## Conventions
- {Most important convention 1}
- {Most important convention 2}
- {Most important convention 3}

Follow existing code patterns. Run tests after changes.
```

## Monorepo Template

For monorepos, the workspace instructions cover shared conventions. Package-specific instructions go in `.instructions.md` files.

```markdown
# Project Guidelines

## Overview
Monorepo managed by {tool — Turborepo/Nx/Lerna/pnpm workspaces}.

## Packages
{List of packages with brief descriptions}

## Shared Conventions
- {Conventions that apply across all packages}

## Build and Test
- **Install**: `{install command}` (from root)
- **Build all**: `{build command}`
- **Test all**: `{test command}`
- **Build specific**: `{package-specific build command pattern}`

## Working in Packages
- Each package may have its own conventions — check its README
- Shared code lives in {shared package path}
- Changes to shared packages require running tests across all dependents
```

## Customization Notes

1. **Link, don't embed**: Reference existing docs (`See [CONTRIBUTING.md](CONTRIBUTING.md)`) rather than copying content
2. **Minimal by default**: Only include what's relevant to EVERY task. Specific concerns go in `.instructions.md` files.
3. **Actionable commands**: Include exact commands agents can run — they will try to execute these
4. **No obvious instructions**: Don't tell agents to "write clean code" — they already try to. Only include conventions that differ from defaults or that agents would get wrong.
5. **Keep under 200 lines**: Long workspace instructions waste context on every interaction
