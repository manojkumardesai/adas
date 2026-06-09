# ADAS Usage Guide

## Setup

### Prerequisites
- VS Code (latest stable)
- GitHub Copilot extension
- Copilot Chat enabled with agent mode

### Installation

1. Clone the ADAS repository:
   ```bash
   git clone https://github.com/your-org/adas.git ~/Projects/adas
   ```

2. Open your target repo in VS Code

3. Add ADAS to the workspace:
   - **File → Add Folder to Workspace...** → select the `adas` folder
   - This creates a multi-root workspace where both repos are accessible

4. Optionally save the workspace:
   - **File → Save Workspace As...** → save as `my-project.code-workspace`

## Basic Usage

### Full Scan & Generate

1. Open VS Code Chat (⌘⇧I / Ctrl+Shift+I)
2. Select **@adas** from the agent picker dropdown
3. Type your request:
   ```
   Scan my-project and generate a Copilot agent system
   ```
4. ADAS will scan, design, and propose a system
5. Review the proposal and approve/modify as needed
6. ADAS generates all files in your repo's `.github/` directory

### Quick Start with Prompt

Type `/init-adas` in chat to trigger the quick-start prompt. ADAS will ask which workspace folder is the target and run the full workflow.

### Updating Existing Configs

If your repo already has Copilot configs:
```
@adas Update the agent configs for my-project — I've added a new test framework
```

ADAS re-scans and proposes incremental updates.

## Multi-Repo (Polyrepo) Workspaces

When your multi-root workspace contains several independent repos (e.g., `web`, `api`, `shared-lib`), ADAS works across all of them:

1. Add each repo as a workspace folder, **plus** add the generated `.adas-workspace/` folder once it exists.
2. Invoke `@adas` and name the target repos (or say "scan the whole workspace").
3. ADAS scans each repo (L1 context) and builds a cross-repo map (L0) — dependency direction and shared contracts.
4. It proposes: a standalone `.github/` per repo + a `.adas-workspace/` coordinator.

After generation, pick **@workspace-coordinator** for cross-repo tasks. It auto-delegates to repo specialists in parallel (producer-first when repos depend on each other) and returns one consolidated change report.

```
WORKSPACE
├── .adas-workspace/     ← coordinator + cross-repo map + guardrail hooks
├── web/.github/         ← standalone agent system
├── api/.github/         ← standalone agent system
└── shared-lib/.github/  ← standalone agent system
```

## Guardrails — No Auto-Commit

ADAS-generated systems never commit on your behalf. Even fully autonomous, cross-repo work stops at a dirty working tree:

- A `block-git-write` hook deterministically denies any history-writing git command.
- Agents are instructed they have no commit authority; specialists stay inside their own repo.
- You get a consolidated diff + test report, then commit manually (or via **Save to GitHub**).

If an agent ever tries to commit, the hook blocks it and explains why — by design.

## Understanding Generated Files

### Agents (.agent.md)

Generated agents have specific roles and tools:

| Agent | Tools | Purpose |
|-------|-------|---------|
| Planner | read, search, web | Research and plan — read-only |
| Implementer | read, edit, search, execute | Write code, run builds/tests |
| Reviewer | read, search | Review code — read-only |
| Tester | read, edit, search, execute | Write and run tests |
| Docs | read, edit, search | Write documentation |

Agents are connected via **handoffs** — after planning, you can hand off directly to the implementer, then to the reviewer, and so on.

### Skills (SKILL.md)

Skills are reusable workflows shared by multiple agents. For example, a `run-tests` skill can be used by both the tester and reviewer agents. Skills include:
- Procedures (step-by-step instructions)
- Scripts (executable shell scripts)
- References (detailed documentation)

### Instructions (.instructions.md)

Scoped instructions apply to specific file types via `applyTo` patterns:
- `**/*.py` — Python conventions
- `**/*.test.*` — Testing conventions
- `src/api/**` — API patterns

They're loaded automatically when you're working on matching files.

### Prompts (.prompt.md)

Task prompts are quick-start templates for common tasks:
- `/generate-tests` — routes to the tester agent
- `/scaffold-component` — routes to the implementer
- `/create-endpoint` — routes to the implementer

### Hooks (.json)

Lifecycle hooks enforce policy:
- **PostToolUse**: Auto-format after file edits
- **PreToolUse**: Block dangerous commands (rm -rf, force push)

## Tips

### Scaling to Repo Size

ADAS adjusts its output based on your repo's complexity:
- **Small repos** (< 50 files): 2-3 agents, minimal skills
- **Medium repos** (50-500 files): 3-4 agents, relevant skills
- **Large repos** (500+ files): Full agent chain with comprehensive skills and instructions

### Customizing After Generation

Generated files are standard Copilot customization files — you can edit them directly:
- Add more specific instructions to `.instructions.md` files
- Adjust tool sets on agents
- Add new prompts for your team's common tasks
- Modify handoff chains

### Monorepo Support

For monorepos, ADAS generates:
- Root-level `copilot-instructions.md` with shared conventions
- Package-specific `.instructions.md` files with `applyTo` scoped to each package
- Skills that understand the monorepo tool (Turborepo, Nx, etc.)

## Troubleshooting

### ADAS agent doesn't appear in picker
- Ensure the ADAS folder is added to your workspace (multi-root)
- Check that `.github/agents/adas.agent.md` exists in the ADAS folder
- Try reloading VS Code (⌘⇧P → "Reload Window")

### Scanner returns incomplete results
- Ensure the target repo has been cloned with git history (`git clone`, not `--depth 1`)
- Check that the target repo path is correct

### Generated files aren't discovered
- Verify files are in the correct `.github/` subdirectories
- Check YAML frontmatter syntax (use the Diagnostics view: right-click Chat → Diagnostics)
- Ensure `description` fields are keyword-rich
