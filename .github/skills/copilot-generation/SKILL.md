---
name: copilot-generation
description: "Generate VS Code Copilot customization files for a target repository. Use when creating agents, skills, instructions, prompts, hooks, or workspace instructions based on a repo analysis report. Covers all 6 Copilot primitives with templates, quality rules, and interconnection patterns."
---

# Copilot Customization Generation

Generate a complete, interconnected suite of VS Code Copilot customization files for a target repository based on a repo analysis report.

## When to Use

- Generating Copilot agent system files for a target repo
- Deciding which primitives to create based on scan data
- Ensuring generated files follow correct syntax, quality rules, and interconnection patterns

## Procedure

### Step 1 — Determine What to Generate

Based on the scan report, decide which files are needed using these signal rules:

#### Agents (always generate at least planner + implementer)
| Signal | Agent to Create | Model |
|--------|-----------------|-------|
| Always | `planner.agent.md` — read-only research and planning | `Claude Sonnet 4.5 (copilot)` |
| Always | `implementer.agent.md` — code writing and editing | `GPT-5.3-Codex (copilot)` |
| Test framework detected | `tester.agent.md` — test writing and execution | `GPT-5.3-Codex (copilot)` |
| CI/CD or deploy config detected | `deployer.agent.md` — deployment workflows | `GPT-5.3-Codex (copilot)` |
| `docs/` folder or extensive README | `docs.agent.md` — documentation writing | `Claude Sonnet 4.5 (copilot)` |
| Large codebase (500+ files) or security patterns | `reviewer.agent.md` — code review and security | `Claude Sonnet 4.5 (copilot)` |

#### Skills (generate when workflow is multi-step or shared)
| Signal | Skill to Create |
|--------|-----------------|
| Test framework detected | `run-tests` — shared by tester + reviewer agents |
| Lint/format tools detected | `lint-format` — shared across all editing agents |
| CI/CD detected | `deploy` — used by deployer agent |
| DB/migration patterns detected | `db-migrations` — used by implementer agent |

#### Instructions (generate per concern)
| Signal | Instruction to Create |
|--------|----------------------|
| Primary language detected | `{language}-conventions.instructions.md` with `applyTo` for language files |
| Test framework detected | `testing.instructions.md` with `applyTo: "**/*.test.*"` or equivalent |
| API routes/endpoints detected | `api-patterns.instructions.md` with `applyTo` for API directory |
| Component patterns (React/Vue/etc.) | `component-patterns.instructions.md` with `applyTo` for component files |
| **Next.js detected (any router)** | `nextjs-conventions.instructions.md` with `applyTo: "**/*.tsx"` — RSC patterns, routing, data fetching |
| **Next.js App Router + `app/` dir** | `server-components.instructions.md` with `applyTo: "app/**"` — async components, streaming, Server Actions |
| **Next.js + `app/api/` detected** | `api-routes.instructions.md` with `applyTo: "app/api/**"` — Route Handlers, request/response patterns |
| **Next.js + feature/module dirs** | `north-star-architecture.instructions.md` — feature boundaries, colocation rules, shared module patterns |
| Security patterns detected | `security.instructions.md` — on-demand, no applyTo |
| DB patterns detected | `database.instructions.md` — on-demand or scoped |

#### Prompts (generate for common tasks)
| Signal | Prompt to Create |
|--------|-----------------|
| Test framework detected | `generate-tests.prompt.md` routed to tester agent |
| Component framework detected | `scaffold-component.prompt.md` routed to implementer |
| API framework detected | `create-endpoint.prompt.md` routed to implementer |
| Always | `review-code.prompt.md` routed to reviewer (if exists) or planner |
| Docs folder detected | `generate-docs.prompt.md` routed to docs agent |

#### Hooks (generate conservatively)
| Signal | Hook to Create |
|--------|---------------|
| Formatter config exists (prettier, black, rustfmt) | `post-edit-format.json` — PostToolUse on edit |
| Dangerous command risk | `block-dangerous.json` — PreToolUse on execute |

#### Workspace Instructions (always generate)
Always generate `copilot-instructions.md` as the foundation.

### Step 2 — Design Interconnections

Before generating, plan the connections:

1. **Handoff chains**: Design agent-to-agent flow
   - Typical: Planner → Implementer → Reviewer → Tester
   - Each agent's `handoffs:` field points to the next agent(s)
2. **Skill sharing**: Map which agents use which skills
   - Skills are discovered by description — ensure agent instructions reference the skill's purpose
3. **Prompt routing**: Each prompt's `agent:` field points to the correct agent
4. **Instruction discovery**: Ensure instruction `description` fields contain keywords agents will encounter

### Step 3 — Generate Files

Use the reference templates for each primitive type:

- [Agent templates](./references/agent-templates.md)
- [Skill templates](./references/skill-templates.md)
- [Instruction templates](./references/instruction-templates.md)
- [Prompt templates](./references/prompt-templates.md)
- [Hook templates](./references/hook-templates.md)
- [Workspace instruction templates](./references/workspace-templates.md)

Generate in dependency order:
1. `copilot-instructions.md` (foundation — other files reference project context from here)
2. `.github/instructions/` (conventions — agents discover these)
3. `.github/skills/` (capabilities — agents reference these)
4. `.github/agents/` (roles — reference skills, have handoffs)
5. `.github/prompts/` (entry points — route to agents)
6. `.github/hooks/` (enforcement — independent)

### Step 4 — Validate Cross-References

After generation, verify:
- [ ] Every `handoffs.agent` value matches an existing `.agent.md` filename (minus extension)
- [ ] Every `agent:` in prompts matches an existing agent name
- [ ] Every skill `name` matches its folder name
- [ ] All `applyTo` globs match actual file patterns in the repo
- [ ] No duplicate or conflicting instructions
- [ ] Descriptions are keyword-rich and contain "Use when..." patterns
- [ ] Tool sets are minimal — each agent has only the tools it needs

## Quality Rules

1. **Descriptions are the discovery surface** — include trigger phrases that agents will encounter
2. **One concern per instruction file** — never mix testing + API + styling
3. **Minimal tool sets** — planner: `[read, search, web]`, implementer: `[read, edit, search, execute]`, reviewer: `[read, search]`
4. **Model tiers** — analytical agents (planner, reviewer, docs): `Claude Sonnet 4.5 (copilot)`; coding agents (implementer, tester, deployer): `GPT-5.3-Codex (copilot)`
5. **Link, don't embed** — reference existing project docs rather than copying content
6. **Quote values with colons** — `description: "Use when: doing X"` not `description: Use when: doing X`
7. **Skills have folder discipline** — `name` must match folder, use `./` for relative paths
8. **Scale to complexity** — small repos get 2-3 agents, large repos get the full chain
9. **Single agent option** — if user prefers, generate one `{project-name}.agent.md` using the consolidated template with `model: "Claude Sonnet 4.5 (copilot)"` and all tools; all prompts route to it
