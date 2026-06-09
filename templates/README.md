# Templates — Shape References (not stack presets)

> **ADAS is tech-agnostic.** These templates exist to show the *structure* of generated files — the frontmatter shape, section ordering, and interconnection style — **not** to bias generation toward a particular stack.

## How ADAS uses these

- **Topology** (agent roles, handoff chains, subagent graph, tool sets) is universal and comes from the generation skill.
- **Content** (commands, conventions, `applyTo` globs, architecture) comes **only** from the scanner's report for the target repo — never copied from these templates.

When generating for a Rust, Go, Elixir, or any other repo, ADAS reads the scan and fills real values. It does **not** match the repo to one of these folders.

## What's here

| Folder | Purpose |
|---|---|
| `_shape/` | Language-neutral skeletons showing structure only (placeholders, no stack content) |
| `typescript-react/` | Worked example output for a TS/React repo — illustrative |
| `python-fastapi/` | Worked example output for a FastAPI repo — illustrative |

Treat `typescript-react/` and `python-fastapi/` as *filled examples* to understand the output format. The canonical structure lives in `_shape/` and in the generation skill references under `.github/skills/copilot-generation/references/`.
