# ADAS Project Context

This is the **ADAS (Advanced Developer Assistance System)** repository — a meta-agent system that generates Copilot customization files for other repositories.

## Important
- This repo contains ADAS agent definitions and templates — do NOT attempt to generate Copilot configs for this repo itself
- The `templates/` directory contains example outputs — these are references, not active configurations
- When modifying ADAS agents or skills, follow the Copilot customization syntax strictly (see `.github/instructions/copilot-syntax.instructions.md`)

## Structure
- `.github/agents/` — ADAS agent definitions (adas.agent.md, adas-scanner.agent.md)
- `.github/skills/` — ADAS skills (repo-analysis, copilot-generation)
- `.github/instructions/` — Format reference for Copilot file syntax
- `.github/prompts/` — Quick-start prompt (init-adas)
- `templates/` — Example generated outputs for common tech stacks
- `docs/` — Usage documentation

## Usage
ADAS is used in a multi-root VS Code workspace alongside a target repository. Users invoke `@adas` to scan the target repo and generate a complete Copilot agent system.
