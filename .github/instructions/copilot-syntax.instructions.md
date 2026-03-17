---
description: "Use when writing or validating VS Code Copilot customization files — agents (.agent.md), skills (SKILL.md), instructions (.instructions.md), prompts (.prompt.md), hooks (.json), or workspace instructions (copilot-instructions.md). Covers correct YAML frontmatter syntax, required fields, valid tool aliases, and common pitfalls."
---
# Copilot Customization File Syntax Reference

## YAML Frontmatter Rules

All customization files (except hooks) use YAML frontmatter between `---` markers:

```yaml
---
key: value
key: "value with: colons must be quoted"
key: [array, of, values]
key: ['quoted items', 'with special chars']
---
```

**Critical rules:**
- Use spaces for indentation — NEVER tabs
- Quote values containing colons: `description: "Use when: writing tests"` NOT `description: Use when: writing tests`
- Arrays can use `[inline]` or multiline `- item` syntax
- Strings with special characters (`#`, `{`, `}`, `[`, `]`, `,`, `&`, `*`, `?`, `|`, `-`, `<`, `>`, `=`, `!`, `%`, `@`, `` ` ``) must be quoted

## File Type Quick Reference

### .agent.md
```yaml
---
description: "<required — keyword-rich for subagent discovery>"
name: "Agent Name"              # Optional, defaults to filename
tools: [read, search, execute]  # Optional: tool aliases, MCP (<server>/*), extension tools
model: "Claude Sonnet 4"       # Optional: single or array for fallback
agents: [scanner, reviewer]     # Optional: allowed subagents (omit = all, [] = none)
user-invocable: true            # Optional: show in agent picker (default: true)
disable-model-invocation: false # Optional: prevent subagent invocation (default: false)
argument-hint: "Describe task"  # Optional: input guidance in chat
handoffs:                       # Optional: transitions to other agents
  - label: "Next Step"
    agent: other-agent
    prompt: "Continue with..."
    send: false                 # false = user reviews, true = auto-submit
    model: "GPT-5 (copilot)"   # Optional: model for handoff
---
```

**Tool aliases:** `execute`, `read`, `edit`, `search`, `agent`, `web`, `todo`
**Special:** `tools: []` = no tools. Omit `tools` = defaults.

### SKILL.md
```yaml
---
name: skill-name                    # Required: 1-64 chars, lowercase alphanumeric + hyphens, MUST match folder name
description: "What and when. Max 1024 chars."
argument-hint: "Optional hint"      # Optional: shown for slash invocation
user-invocable: true                # Optional: show as / command (default: true)
disable-model-invocation: false     # Optional: disable auto-loading (default: false)
---
```

**Folder structure:** `.github/skills/{skill-name}/SKILL.md` — `name` field = folder name.
**Progressive loading:** SKILL.md body < 500 lines. Use `references/` for detailed docs.
**Relative paths:** Always use `./` — `[script](./scripts/run.sh)`

### .instructions.md
```yaml
---
description: "<required — keyword-rich, 'Use when...' pattern>"
name: "Instruction Name"           # Optional
applyTo: "**/*.ts"                 # Optional: auto-attach for matching files
---
```

**applyTo patterns:**
- `"**/*.py"` — all Python files
- `["src/**", "lib/**"]` — multiple patterns (OR)
- `"src/api/**/*.ts"` — specific folder + extension
- Avoid `"**"` unless it truly applies to ALL files (burns context)

### .prompt.md
```yaml
---
description: "What this prompt does"     # Recommended
name: "Prompt Name"                      # Optional
argument-hint: "Describe input"          # Optional
agent: "agent-name"                      # Optional: ask, agent, plan, or custom agent name
model: "Claude Sonnet 4 (copilot)"      # Optional: or array for fallback
tools: [search, web]                     # Optional
---
```

### copilot-instructions.md
No frontmatter required. Plain markdown. Only one of `copilot-instructions.md` or `AGENTS.md` — not both.

### hooks (.json)
```json
{
  "hooks": {
    "EventName": [
      {
        "type": "command",
        "command": "./scripts/hook.sh",
        "timeout": 15
      }
    ]
  }
}
```

**Events:** `SessionStart`, `UserPromptSubmit`, `PreToolUse`, `PostToolUse`, `PreCompact`, `SubagentStart`, `SubagentStop`, `Stop`
**Exit codes:** 0 = success, 2 = blocking error, other = non-blocking warning

## Common Pitfalls

| Pitfall | Fix |
|---------|-----|
| Unquoted colon in description | `description: "Use when: doing X"` |
| Tabs in YAML | Use spaces only |
| `name` doesn't match folder (skills) | Ensure exact match: `name: run-tests` → folder `run-tests/` |
| `applyTo: "**"` | Use specific globs unless truly universal |
| Vague description | Include trigger keywords: "Use when writing tests, checking coverage, debugging test failures" |
| Too many tools on agent | Minimal set only — planner: `[read, search]`, not `[read, edit, search, execute, web]` |
| Handoff to nonexistent agent | Verify agent names match filenames (minus `.agent.md`) |
| SKILL.md > 500 lines | Move details to `references/` folder |
| Missing `description` | Required for agents, instructions; strongly recommended for prompts and skills |
