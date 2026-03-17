---
name: repo-analysis
description: "Deep repository analysis for generating Copilot customization files. Use when scanning a target repo to understand its tech stack, conventions, build/test workflows, documentation, git history, existing agent configs, and complexity signals."
---

# Repository Analysis

Perform a comprehensive analysis of a target repository to inform Copilot customization file generation.

## When to Use

- Scanning a new repo before generating Copilot agent configs
- Re-scanning a repo to detect changes for incremental updates
- Understanding a codebase's structure, conventions, and tooling

## Procedure

Follow the [analysis checklist](./references/analysis-checklist.md) to systematically scan each category. Return a structured report with findings per category.

### Step 1 — Identify the Target

Confirm the target repo root path. Verify it contains source files (not the ADAS repo itself).

### Step 2 — Run Analysis Categories

Execute each category from the checklist in order:

1. **Tech Stack Detection** — Languages, frameworks, package managers, major dependencies
2. **Code Conventions** — Naming, architecture, error handling, patterns
3. **Build/Test/Deploy** — Commands, CI/CD configs, containers, deploy targets
4. **Documentation Inventory** — README, CONTRIBUTING, docs/, ADRs, changelogs
5. **Git History Analysis** — Commit conventions, branch naming, PR patterns
6. **Existing Agent Configs** — Copilot, Cursor, Claude, Windsurf files already present
7. **Security & Policy** — Auth patterns, secrets management, env handling
8. **Complexity Signals** — File count, monorepo indicators, service boundaries

### Step 3 — Compile Report

Return a structured report using this format:

```
## Scan Report: {repo-name}

### Tech Stack
- **Languages**: {list}
- **Frameworks**: {list}
- **Package Manager**: {name} — {lockfile found}
- **Key Dependencies**: {top 10 most relevant}

### Code Conventions
- **Naming**: {camelCase/snake_case/PascalCase}
- **Architecture**: {MVC/clean/hexagonal/flat/etc.}
- **Error Handling**: {pattern observed}
- **Key Patterns**: {notable patterns}

### Build/Test/Deploy
- **Install**: {command}
- **Build**: {command}
- **Test**: {framework} — {command}
- **Lint/Format**: {tools} — {command}
- **CI/CD**: {platform} — {config path}
- **Containers**: {Dockerfile path or none}
- **Deploy**: {target or unknown}

### Documentation
- **README**: {exists, quality: brief/detailed}
- **CONTRIBUTING**: {exists or not}
- **Docs folder**: {path and contents summary}
- **ADRs**: {found or not}
- **Changelog**: {found or not}

### Git Analysis
- **Commit style**: {conventional/freeform/prefix-based}
- **Branch naming**: {pattern}
- **Recent activity**: {summary of last 20 commits}
- **PR templates**: {found or not}

### Existing Agent Configs
- **copilot-instructions.md**: {found or not}
- **AGENTS.md**: {found or not}
- **.instructions.md files**: {count, list}
- **.agent.md files**: {count, list}
- **SKILL.md files**: {count, list}
- **.prompt.md files**: {count, list}
- **Hooks**: {count, list}
- **Other**: {.cursorrules, CLAUDE.md, .windsurfrules}

### Security & Policy
- **Env handling**: {.env patterns, config management}
- **Auth patterns**: {JWT/OAuth/API keys observed}
- **Secrets management**: {approach if detectable}

### Complexity Signals
- **Source file count**: {approximate}
- **Monorepo**: {yes/no, package count if yes}
- **Service boundaries**: {list if multiple services}
- **Shared libraries**: {list if any}
```
