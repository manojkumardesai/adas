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
