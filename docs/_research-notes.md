# Phase 0 — Research & Schema Lock (findings)

Internal notes confirming the exact VS Code / GitHub Copilot file schemas ADAS generates. Sourced from the official VS Code and GitHub Copilot docs (verified Jun 2026).

## Custom agents (`.agent.md`)

Markdown + YAML frontmatter. Discovered from `<folder>/.github/agents/` per workspace folder, or the user profile.

Key frontmatter:

| Field | Meaning |
|---|---|
| `description` | Discovery surface; keyword-rich, quote values containing colons |
| `tools` | Minimal allowed tools, e.g. `[read, search]` |
| `agents` | Subagent allowlist a parent may delegate to: `*`, named list, or `[]` to block |
| `user-invocable` | `false` hides the agent from the picker (subagent-only) |
| `disable-model-invocation` | Prevents other agents invoking it as a subagent unless parent's `agents` allows |
| `model` | Optional model / prioritized list |
| `handoffs` | UI step transitions (see below) |

## Handoffs (confirmed)

```yaml
handoffs:
  - label: Start Implementation   # button text
    agent: implementer            # target agent identifier (NOT "target")
    prompt: Implement the plan.   # prefilled prompt
    send: false                   # true = auto-submit, false = prefill only
```

ADAS already uses the correct `agent:` key. Verification rule stays: every `handoffs[].agent` must resolve to an existing agent.

## Subagents vs handoffs (orchestration)

- **Subagents**: isolated-context delegated workers; return only their result; can run **in parallel** (VS Code Feb–Jun 2026). Controlled via `agents` allowlist + `user-invocable: false`.
- **Handoffs**: UI-driven, human-clicked stage transitions. Use for deliberate checkpoints (plan → implement → review).

## Hooks (`.json`) — IMPORTANT CORRECTION

VS Code parses the Claude Code hook config. Two facts that change ADAS's design:

1. **`matcher` is recognized but IGNORED at runtime** in current VS Code Preview. Do **not** rely on `matcher` to scope a hook to a tool. Filter **inside the script** using the stdin JSON payload.
2. Blocking is done by the **script's output** (`hookSpecificOutput.permissionDecision: "deny"`), not a declarative `action`/`condition` field.

Documented config shape (the style ADAS already uses, kept for consistency):

```json
{
  "hooks": {
    "PreToolUse": [
      { "type": "command", "command": "./scripts/<name>.sh", "timeout": 5 }
    ]
  }
}
```

GitHub Copilot CLI also documents a native variant with `version: 1`, `bash`/`powershell`, and `timeoutSec`; VS Code normalizes lowerCamelCase event names to PascalCase. ADAS standardizes on the `command` + `permissionDecision`-output style to match the existing `block-dangerous` template.

Script contract (deny path):
```bash
echo '{"hookSpecificOutput":{"hookEventName":"PreToolUse","permissionDecision":"deny","permissionDecisionReason":"<why>"}}'
```
Allow path emits `"permissionDecision":"allow"`.

## `context.md` section contract (L1, Phase 0.4)

Stable headings agents key off (machine-parseable). Order fixed:

```
# Repo Context: <name>
## Summary            (1–3 lines)
## Tech Stack         (table)
## Commands           (install/build/test/lint/run — exact)
## Conventions        (naming/architecture/error-handling)
## Architecture       (text + one flowchart mermaid)
## Key Flows          (optional sequenceDiagram)
## Capabilities       (detected: test-runner/formatter/ci/api/ui/db — drives agent selection)
## Entry Points       (key files/dirs)
## Docs               (links, not copies)
## Scan Metadata      (file count, complexity tier, scan date, commit)
```

Multi-repo adds an L0 `workspace-map.md` (see workspace-map-template.md).

## Decisions

- Mermaid: supplement only, drawn where info is a graph (architecture, flows, pipeline, agent topology, cross-repo deps). Flat facts stay as text/tables.
- Coordinator workspace review report: rendered in chat AND not auto-written to a file by default (avoids spurious working-tree changes that the guardrail would then flag). Optional `--write-report` later.
