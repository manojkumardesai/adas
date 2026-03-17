# ADAS — Advanced Developer Assistance System

A meta-agent that analyzes any repository and generates a complete, interconnected VS Code Copilot agent system.

## What ADAS Does

You point ADAS at a repo, and it generates:

| What | Files | Purpose |
|------|-------|---------|
| **Workspace instructions** | `copilot-instructions.md` | Project-wide foundation for all agents |
| **Scoped instructions** | `.instructions.md` files | Per-concern rules (testing, API, components) with file matching |
| **Reusable skills** | `SKILL.md` + scripts | Multi-step workflows shared across agents |
| **Specialized agents** | `.agent.md` files | Role-based personas (planner, implementer, reviewer, tester) with handoffs |
| **Task prompts** | `.prompt.md` files | One-shot tasks routed to the right agent |
| **Lifecycle hooks** | `.json` configs | Deterministic enforcement (auto-format, block dangerous commands) |

The generated files form an **interconnected system** — agents hand off to each other, share skills, and prompts route to the right specialist.

## Quick Start

### 1. Clone ADAS

```bash
git clone https://github.com/your-org/adas.git
```

### 2. Open Multi-Root Workspace

Open VS Code and create a multi-root workspace with both ADAS and your target repo:

1. **File → Add Folder to Workspace...** → select the ADAS folder
2. **File → Add Folder to Workspace...** → select your target repo
3. Save the workspace file (optional, but recommended)

Your workspace should look like:

```
WORKSPACE
├── adas/          ← ADAS meta-agent system
└── your-repo/     ← Target repository
```

### 3. Invoke ADAS

In the VS Code Chat panel:

1. Select **@adas** from the agent picker
2. Type: `Scan my-repo and generate a Copilot agent system`
3. Or use the quick-start prompt: type `/init-adas` in chat

ADAS will:
1. **Scan** your repo (tech stack, conventions, build/test, docs, git history)
2. **Design** an interconnected agent system tailored to your repo
3. **Propose** the system for your review (you approve, modify, or skip files)
4. **Generate** all approved files in your repo's `.github/` directory

### 4. Use the Generated Agents

After generation, switch to any generated agent in the chat agent picker:
- **@planner** — research and plan before coding
- **@implementer** — write code following project conventions
- **@reviewer** — review code for quality and security
- **@tester** — write and run tests

Use handoff buttons to flow between agents (e.g., Plan → Implement → Review → Test).

## What Gets Generated

ADAS analyzes your repo and generates files tailored to your specific tech stack, conventions, and workflows. Here's an example for a React/TypeScript project:

```
your-repo/.github/
├── copilot-instructions.md                          # Project overview, stack, commands
├── instructions/
│   ├── typescript-react.instructions.md             # TS/React conventions (applyTo: **/*.tsx)
│   └── testing.instructions.md                      # Vitest patterns (applyTo: **/*.test.*)
├── agents/
│   ├── planner.agent.md                             # Read-only planning → handoff to implementer
│   ├── implementer.agent.md                         # Code writing → handoff to reviewer
│   ├── reviewer.agent.md                            # Code review → handoff to tester
│   └── tester.agent.md                              # Test writing and execution
├── skills/
│   └── run-tests/
│       └── SKILL.md                                 # Shared test execution skill
├── prompts/
│   ├── generate-tests.prompt.md                     # → routes to @tester
│   └── scaffold-component.prompt.md                 # → routes to @implementer
└── hooks/
    └── post-edit-format.json                        # Auto-format after edits
```

## Updating Existing Configs

If your repo already has Copilot configuration files, ADAS enters **update mode**:
- Scans existing configs and identifies gaps
- Proposes only new or modified files
- Shows diffs for changed files
- Never deletes existing configs without permission

## Templates

The `templates/` directory contains complete example outputs for common stacks:
- `templates/typescript-react/` — React + TypeScript + Vitest + Tailwind
- `templates/python-fastapi/` — FastAPI + SQLAlchemy + pytest

These serve as references for what ADAS generates and can help you understand the output format.

## ADAS Architecture

ADAS itself is built using VS Code Copilot customization files:

| File | Role |
|------|------|
| `.github/agents/adas.agent.md` | Main orchestrator — user-facing workflow |
| `.github/agents/adas-scanner.agent.md` | Read-only repo analysis sub-agent |
| `.github/skills/repo-analysis/` | Deep analysis procedures and checklist |
| `.github/skills/copilot-generation/` | Generation templates for all 6 primitives |
| `.github/instructions/copilot-syntax.instructions.md` | Copilot file format reference |
| `.github/prompts/init-adas.prompt.md` | Quick-start prompt |

## Requirements

- VS Code with GitHub Copilot
- Agent mode enabled in Copilot settings
- Multi-root workspace support

## License

MIT
