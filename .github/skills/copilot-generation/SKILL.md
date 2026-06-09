---
name: copilot-generation
description: "Generate VS Code Copilot customization files for a target repository or multi-repo workspace. Use when creating agents, skills, instructions, prompts, hooks, workspace instructions, context artifacts, or a workspace coordinator based on a repo analysis report. Tech-agnostic — content comes from the scan, never from stack presets. Covers all 6 Copilot primitives plus the persisted context layer and guardrails."
---

# Copilot Customization Generation

Generate a complete, interconnected suite of VS Code Copilot customization files for a target repo (or multi-repo workspace) based on a repo analysis report.

## Core Principle — Topology vs Content

ADAS is **tech-agnostic**. Separate the two halves of every generated system:

- **Topology** (stack-independent): agent roles, handoff chains, subagent graph, tool sets, guardrails. These come from this skill and are the same for Rust, Go, Python, or anything else.
- **Content** (stack-dependent): build/test/lint/run commands, `applyTo` globs, naming conventions, architecture, capabilities. These come **only** from the scan report for the target repo — never copied from a template or assumed from the language.

**Fail fast, don't fill blanks.** If a signal is missing from the scan, omit the file (or write `None detected`) — never emit generic boilerplate or guess a command. The `templates/` folder shows *shape*, not content.

## When to Use

- Generating a Copilot agent system for a target repo or polyrepo workspace
- Deciding which primitives to create based on detected **capabilities**
- Ensuring generated files follow correct syntax, quality rules, interconnection, and guardrails

## Procedure

### Step 1 — Determine What to Generate (capability-driven)

Choose files by detected **capability**, independent of language. Always generate planner + implementer + the context layer + guardrails.

#### Always
- `copilot-instructions.md` — workspace foundation
- `.github/adas/context.md` — persisted L1 scan grounding (see [context templates](./references/context-templates.md))
- `project-context.instructions.md` — always-on, points agents at `context.md` (see [instruction templates](./references/instruction-templates.md))
- `planner.agent.md`, `implementer.agent.md`
- `hooks/block-git-write.json` + `hooks/block-dangerous.json` — guardrails (see [hook templates](./references/hook-templates.md))

#### Capability → file
| Detected capability | Generate |
|---|---|
| test-runner | `tester.agent.md` + `run-tests` skill + `testing.instructions.md` + `generate-tests.prompt.md` |
| formatter/linter | `post-edit-format.json` hook (+ `lint-format` skill if multi-step) |
| ci/cd | `deployer.agent.md` + `deploy` skill |
| api-surface (routes/OpenAPI/proto) | `api-patterns.instructions.md` + `create-endpoint.prompt.md` |
| ui-framework | `component-patterns.instructions.md` + `scaffold-component.prompt.md` |
| db/migrations | `db-migrations` skill + `database.instructions.md` |
| large codebase (500+) or security patterns | `reviewer.agent.md` + `review-code.prompt.md` |
| docs/ folder or extensive README | `docs.agent.md` + `generate-docs.prompt.md` |
| primary language | `{language}-conventions.instructions.md` |

**Scale to complexity:** small (<50 files) 2–3 agents; medium (50–500) 3–4; large (500+) full chain.

### Step 2 — Design Interconnections

1. **Handoff chains** (human gates): Planner → Implementer → Reviewer → Tester. Each agent's `handoffs[].agent` points to the next existing agent.
2. **Subagent graph** (auto fan-out): any delegating agent sets `agents:` to the explicit allowlist it may invoke. Specialists that are subagent-only get `user-invocable: false`.
3. **Skill sharing**: map which agents use which skills; ensure agent bodies reference the skill's purpose.
4. **Prompt routing**: each prompt's `agent:` points to a real agent.
5. **Context grounding**: every agent inherits `project-context.instructions.md` → reads `context.md`.

Subagents vs handoffs: **subagents for autonomous fan-out, handoffs for human checkpoints.**

### Step 3 — Generate Files

Use the reference templates as **shape** (fill with scan content):
- [Agent templates](./references/agent-templates.md)
- [Skill templates](./references/skill-templates.md)
- [Instruction templates](./references/instruction-templates.md)
- [Prompt templates](./references/prompt-templates.md)
- [Hook templates](./references/hook-templates.md)
- [Workspace instruction templates](./references/workspace-templates.md)
- [Context templates (L1)](./references/context-templates.md)
- [Workspace map template (L0)](./references/workspace-map-template.md)

Generate in dependency order:
1. `.github/copilot-instructions.md` (foundation)
2. `.github/adas/context.md` (grounding — agents reference it)
3. `.github/instructions/` (incl. always-on `project-context.instructions.md`)
4. `.github/skills/`
5. `.github/agents/` (reference skills, have handoffs + `agents` allowlists)
6. `.github/prompts/`
7. `.github/hooks/` (incl. `block-git-write.json`; `chmod +x` scripts)

### Step 3b — Multi-Repo (polyrepo) generation

If the scan reports **polyrepo** topology:
1. Generate a full standalone `.github/` for **each** repo (steps 1–7), with per-repo specialists set `user-invocable: false`.
2. Generate `.adas-workspace/`:
   - `.github/agents/workspace-coordinator.agent.md` (`user-invocable: true`, `agents:` = all repo specialists, auto-delegate) — see [agent templates](./references/agent-templates.md)
   - `.github/hooks/block-git-write.json` + `block-dangerous.json`
   - `context/workspace-map.md` (L0) — see [workspace map template](./references/workspace-map-template.md)
   - `context/<repo>.context.md` cached copies (optional)
3. Tell the user to **add `.adas-workspace/` as a folder in the multi-root workspace** so VS Code discovers its agents/hooks.

For **monorepo**: one root `copilot-instructions.md` + package-scoped `.instructions.md` (with `applyTo` per package); no `.adas-workspace/`.

### Step 4 — Validate Cross-References

- [ ] Every `handoffs[].agent` resolves to an existing `.agent.md`
- [ ] Every `agents:` allowlist entry resolves to an existing agent (incl. cross-repo for the coordinator)
- [ ] Every prompt `agent:` matches an agent name
- [ ] Every skill `name` matches its folder name
- [ ] All `applyTo` globs match real file patterns in the repo
- [ ] `project-context.instructions.md` exists and `context.md` is present
- [ ] **Guardrail present**: `block-git-write.json` (+ script, executable) in every generated set
- [ ] **No agent grants commit authority** (no editing agent body permits git commit/push)
- [ ] Polyrepo: each repo `.github/` is standalone; specialists are `user-invocable: false`
- [ ] Descriptions keyword-rich; tool sets minimal; no YAML colon/tab issues

## Quality Rules

1. **Topology vs content** — never bake stack-specific content into the generation path; pull it from the scan.
2. **Fail fast** — omit a file when its signal is absent; no boilerplate, no guessed commands.
3. **Descriptions are the discovery surface** — include trigger phrases agents will encounter.
4. **One concern per instruction file** — never mix testing + API + styling.
5. **Minimal tool sets** — planner `[read, search, web]`, implementer `[read, edit, search, execute]`, reviewer `[read, search]`.
6. **Guardrail always** — every set ships `block-git-write`; agents stop at a dirty working tree; the human commits.
7. **Link, don't embed** — reference existing docs and `context.md` rather than copying.
8. **Quote values with colons** — `description: "Use when: doing X"`.
9. **Skills have folder discipline** — `name` matches folder; use `./` for relative paths.
10. **Scale to complexity** — small repos get a lean system, not the full chain.
