# Context Templates (L1 — per-repo)

Template for the durable repo context artifact ADAS persists at `<repo>/.github/adas/context.md`. This is **grounding** for the generated agents (referenced by an always-on instruction), not an ephemeral scan dump.

## Rules

- **Stable headings** — keep the exact section names/order below; agents key off them.
- **Real values only** — every field is filled from the scan. If a signal is missing, write `None detected` (never invent or copy from another repo).
- **Mermaid is selective** — draw a diagram only where the information *is* a graph (architecture, key flows). Flat facts stay as tables/text.
- **Link, don't embed** — point at existing docs rather than copying them.

## `<repo>/.github/adas/context.md`

```markdown
# Repo Context: {repo-name}

## Summary
{1–3 line description of what this repo is and does}

## Tech Stack
| Layer | Technology | Version/Notes |
|-------|-----------|---------------|
| Language | {lang} | {version} |
| Framework | {framework} | {version} |
| Package manager | {pm} | {lockfile} |
| Datastore | {db or None detected} | |

## Commands
- **Install**: `{cmd}`
- **Build**: `{cmd or None detected}`
- **Test**: `{cmd or None detected}`
- **Lint/Format**: `{cmd or None detected}`
- **Run/Dev**: `{cmd or None detected}`

## Conventions
- **Naming**: {per-entity convention}
- **Architecture**: {MVC / clean / feature-based / flat} — evidence: {paths}
- **Error handling**: {pattern}
- **Imports**: {organization}

## Architecture
{2–4 sentences. Then a flowchart ONLY if modules have meaningful dependencies.}

```mermaid
flowchart TD
    {Module} --> {Module}
```

## Key Flows
{Optional. Include a sequenceDiagram for the primary request/auth/data flow if one exists. Omit if trivial.}

## Capabilities
> Drives agent/skill/instruction selection. Mark each detected/not.
- test-runner: {framework or None}
- formatter/linter: {tool or None}
- ci/cd: {platform+path or None}
- api-surface: {routes/OpenAPI/proto path or None}
- ui-framework: {framework or None}
- db/migrations: {tool or None}

## Entry Points
- {path} — {what it is}

## Docs
- {[README](../README.md), CONTRIBUTING, docs/… — links only}

## Scan Metadata
- Source files (approx): {n}
- Complexity tier: {small <50 / medium 50–500 / large 500+}
- Scanned: {ISO date}
- Commit: {short sha}
```

## Generation notes

1. Created during Phase 5 of the ADAS workflow, **before** agents (agents reference it).
2. Pair with a `project-context.instructions.md` (always-on, no `applyTo`) that tells agents to read this file first.
3. On update mode, regenerate `context.md` and show a diff; never silently overwrite hand edits without surfacing the change.
