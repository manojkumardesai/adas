# Phase 6 — Dry-Run Validation

Mental walk-throughs confirming ADAS produces correct, interconnected, guardrailed systems across stacks and topologies. No code runs — these validate the generation logic against the skills and templates.

## Scenario A — Single repo, Rust CLI (tech-agnostic check)

**Scan signals:** `Cargo.toml`, `cargo test`, `rustfmt.toml`, no UI, no API, no CI, ~40 files (small).

**Expected output (`<repo>/.github/`):**
- `copilot-instructions.md` (Rust, `cargo build/test`, `cargo fmt`)
- `adas/context.md` (Rust stack, commands, module flowchart) + `project-context.instructions.md`
- `instructions/rust-conventions.instructions.md` (`applyTo: "**/*.rs"`)
- `instructions/testing.instructions.md` (`applyTo` test pattern)
- `skills/run-tests/` (script: `cargo test`)
- `agents/`: planner, implementer, tester (small repo → no reviewer/docs/deployer)
- `hooks/`: `block-git-write.json` (+ script), `block-dangerous.json`, `post-edit-format.json` (`cargo fmt`)

**Checks:** ✅ no TS/Python content anywhere; ✅ commands are real `cargo` commands; ✅ guardrail present; ✅ handoffs resolve (planner→implementer→tester).

## Scenario B — pnpm monorepo (web + api + ui-kit)

**Scan signals:** `pnpm-workspace.yaml`, packages with own manifests, Vitest, Biome, GitHub Actions, ~600 files (large).

**Expected output:**
- Root `copilot-instructions.md` (monorepo, pnpm, turbo/pnpm commands)
- `adas/context.md` with architecture flowchart across packages
- Package-scoped instructions via `applyTo`: `packages/web/**`, `packages/api/**`, `packages/ui-kit/**`
- `skills/run-tests` (vitest), `lint-format` (biome), `deploy` (Actions)
- Full agent chain: planner, implementer, reviewer (large), tester, deployer (ci), docs
- Hooks: `block-git-write`, `block-dangerous`, `post-edit-format` (biome)

**Checks:** ✅ one root system, no `.adas-workspace/` (single git repo); ✅ `applyTo` globs match package dirs; ✅ subagent `agents:` allowlists resolve.

## Scenario C — Polyrepo (web + api + shared-lib)

**Scan signals:** three independent git repos; `web` depends on `api` (OpenAPI at `api/openapi.yaml`) and on `shared-lib` (npm dep); `api` depends on `shared-lib`.

**Expected output:**
- `web/.github/`, `api/.github/`, `shared-lib/.github/` — each a standalone system; specialists `user-invocable: false`
- `.adas-workspace/`:
  - `.github/agents/workspace-coordinator.agent.md` — `user-invocable: true`, `agents: [shared-lib-implementer, api-implementer, web-implementer, ...]`, auto-delegate
  - `.github/hooks/block-git-write.json` + `block-dangerous.json`
  - `context/workspace-map.md` — repos table + cross-repo `flowchart` (`web→api`, `web→shared-lib`, `api→shared-lib`) + contracts + producer-first order (shared-lib → api → web)

**Checks:** ✅ topology = polyrepo; ✅ coordinator `agents:` resolve across repos; ✅ delegation order is producer-first; ✅ each repo standalone; ✅ guardrail in every set + `.adas-workspace/`; ✅ a cross-repo change (add field to user) delegates api then web, returns consolidated report, commits nothing.

## Guardrail spot-check (all scenarios)

- `block-git-write.sh` denies: `git commit -m ...`, `git push`, `git add -A && git commit ...`, `git reset --hard`, `git clean -fd`, `git rebase`.
- Allows: `git status`, `git diff`, `git log`.
- No editing agent body contains language permitting a commit.

## Result

All three scenarios produce valid, interconnected, tech-agnostic, guardrailed systems. Generation logic in `copilot-generation/SKILL.md` + scan structure in `repo-analysis/SKILL.md` cover single / monorepo / polyrepo without stack presets.
