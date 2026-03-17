# Hook Templates

Templates for generating `.json` hook configuration files. Generate hooks conservatively — only for tooling the repo already uses.

## Post-Edit Format Hook

Automatically runs the formatter after file edits. Only generate if a formatter config exists (prettier, black, rustfmt, gofmt, biome).

### hooks/post-edit-format.json

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "type": "command",
        "command": "{format_command}",
        "timeout": 30
      }
    ]
  }
}
```

### Format Commands by Stack

| Stack | Config File | Command |
|-------|------------|---------|
| Node.js + Prettier | `.prettierrc*`, `prettier.config.*` | `npx prettier --write .` |
| Node.js + Biome | `biome.json` | `npx biome check --write .` |
| Python + Black | `pyproject.toml` `[tool.black]` | `black .` |
| Python + Ruff | `ruff.toml`, `pyproject.toml` `[tool.ruff]` | `ruff format .` |
| Go | (built-in) | `gofmt -w .` |
| Rust | `rustfmt.toml` | `cargo fmt` |

## Block Dangerous Commands Hook

Prevents accidental destructive operations. Generate for any project.

### hooks/block-dangerous.json

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "type": "command",
        "command": "./scripts/check-dangerous.sh",
        "timeout": 5
      }
    ]
  }
}
```

### scripts/check-dangerous.sh

```bash
#!/usr/bin/env bash
# Reads tool input from stdin, blocks dangerous commands
set -euo pipefail

INPUT=$(cat)
TOOL_NAME=$(echo "$INPUT" | grep -o '"toolName":"[^"]*"' | cut -d'"' -f4 2>/dev/null || echo "")

if [ "$TOOL_NAME" = "terminal" ] || [ "$TOOL_NAME" = "execute" ]; then
  COMMAND=$(echo "$INPUT" | grep -o '"command":"[^"]*"' | cut -d'"' -f4 2>/dev/null || echo "")

  # Block patterns
  if echo "$COMMAND" | grep -qE '(rm\s+-rf\s+[/~]|git\s+push\s+.*--force|git\s+reset\s+--hard|drop\s+database|drop\s+table|truncate\s+table)'; then
    echo '{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"deny","permissionDecisionReason":"Blocked: destructive command detected"}}'
    exit 0
  fi
fi

echo '{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"allow"}}'
```

## Session Start Context Hook

Injects project context at the start of each agent session. Useful for large repos.

### hooks/session-context.json

```json
{
  "hooks": {
    "SessionStart": [
      {
        "type": "command",
        "command": "./scripts/inject-context.sh",
        "timeout": 10
      }
    ]
  }
}
```

### scripts/inject-context.sh

```bash
#!/usr/bin/env bash
set -euo pipefail

# Inject project summary into agent context
cat <<'EOF'
{
  "systemMessage": "Project context: {project_summary}"
}
EOF
```

## Customization Notes

1. **Conservative generation**: Only generate format hooks if the repo has a formatter config. Only generate block-dangerous if the repo uses terminal tools.
2. **Replace placeholders**: `{format_command}` → actual detected command. `{project_summary}` → brief project description from scan.
3. **Test scripts work**: Generated scripts must be executable. Run `chmod +x` after creation.
4. **Keep hooks fast**: Timeout should be 5-30 seconds. Long hooks block the agent.
5. **No secrets in hooks**: Never embed API keys, tokens, or credentials in hook scripts.
6. **Platform compatibility**: Use `#!/usr/bin/env bash` for portability. If the repo has Windows users, add `"windows"` overrides.
