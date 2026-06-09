# `_shape/` — Language-Neutral Skeletons

These show the **structure** of each generated file with placeholders only — no stack content. ADAS fills placeholders from the scan. They exist so you can see the shape independent of any language. The two sibling folders (`typescript-react/`, `python-fastapi/`) are *filled examples* of this shape.

## copilot-instructions.md
```markdown
# {project-name}
{1–2 line overview from README}

## Stack
{languages, frameworks, package manager}

## Commands
- Install: `{cmd}`
- Build:   `{cmd}`
- Test:    `{cmd}`
- Lint:    `{cmd}`

## Conventions
{only those that differ from language defaults}

## Context
Read `.github/adas/context.md` for full grounding.
```

## .github/adas/context.md
See `.github/skills/copilot-generation/references/context-templates.md` (stable L1 headings).

## instructions/{concern}.instructions.md
```markdown
---
description: "Use when {trigger}. Covers {concern} for this project."
applyTo: "{glob}"   # omit for always-on
---
# {Concern}
{real conventions from scan — show, don't tell}
```

## agents/{role}.agent.md
```markdown
---
description: "{role purpose}. Use for {tasks}."
tools: [{minimal set}]
agents: [{subagent allowlist — optional}]
user-invocable: {true|false}
handoffs:
  - label: "{button}"
    agent: {existing-agent}
    prompt: "{prefilled}"
    send: false
---
You are the {role} for this project.
## Role / Constraints / Approach / Output Format
{include the no-commit guardrail clause for editing agents}
```

## skills/{name}/SKILL.md
```markdown
---
name: {name}            # must match folder
description: "Use when {trigger}."
---
# {Name}
## Procedure
{steps using the real detected commands}
```

## hooks/block-git-write.json
See `.github/skills/copilot-generation/references/hook-templates.md` (always generated).
