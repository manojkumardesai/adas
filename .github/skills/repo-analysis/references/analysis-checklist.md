# Analysis Checklist

Detailed procedures for each analysis category. Use targeted file reads — do NOT attempt to read the entire repo.

## 1. Tech Stack Detection

### Files to Check
- `package.json`, `package-lock.json`, `yarn.lock`, `pnpm-lock.yaml` → Node.js/JS/TS
- `pyproject.toml`, `setup.py`, `setup.cfg`, `requirements.txt`, `Pipfile`, `poetry.lock`, `uv.lock` → Python
- `Cargo.toml`, `Cargo.lock` → Rust
- `go.mod`, `go.sum` → Go
- `pom.xml`, `build.gradle`, `build.gradle.kts` → Java/Kotlin
- `Gemfile`, `Gemfile.lock` → Ruby
- `composer.json` → PHP
- `*.csproj`, `*.sln`, `Directory.Build.props` → .NET/C#
- `pubspec.yaml` → Dart/Flutter
- `mix.exs` → Elixir

### Procedure
1. List the repo root directory
2. Check for each package manager file above
3. Read the primary manifest (e.g., `package.json`, `pyproject.toml`) to extract:
   - Dependencies and devDependencies
   - Scripts/commands
   - Project metadata
4. Search for framework indicators:
   - `next.config.*`, `nuxt.config.*`, `vite.config.*`, `webpack.config.*` → Frontend frameworks
   - `tsconfig.json` → TypeScript
   - `angular.json` → Angular
   - `app/` + `routes/` → Remix/Next.js/Rails
   - `src/main.ts` + `@nestjs` → NestJS
   - `manage.py` or `wsgi.py` → Django
   - `main.py` + `fastapi` → FastAPI
   - `main.go` → Go service

5. **Next.js deep detection** (run if `next.config.*` found or `"next"` in `package.json` dependencies):
   ```bash
   # Router type
   ls {target_path}/app 2>/dev/null && echo "App Router" || echo "Pages Router only"
   ls {target_path}/pages 2>/dev/null && echo "Pages Router present"
   # RSC usage
   grep -r '"use client"' {target_path}/app --include="*.tsx" -l 2>/dev/null | wc -l
   grep -r '"use server"' {target_path}/app --include="*.tsx" -l 2>/dev/null | wc -l
   # Architecture
   ls {target_path}/features 2>/dev/null && echo "features/ dir found"
   ls {target_path}/app/api 2>/dev/null && echo "Route Handlers present"
   find {target_path}/app -name "middleware.ts" 2>/dev/null
   ```
   Classify as:
   - **North Star Architecture** → `app/` present + feature directories (`features/` or route groups `app/(group)/`) + collocated components/hooks/actions per feature + RSC-first (fewer `"use client"` than `"use server"` or total components)
   - **App Router standard** → `app/` present, flat or non-feature structure
   - **Pages Router** → only `pages/`, no `app/`
   - **Mixed** → both `app/` and `pages/`

### Output
- Primary language(s) with confidence
- Framework(s) detected
- Package manager + lockfile
- Top 10 key dependencies (by relevance, not alphabetical)

## 2. Code Conventions

### Procedure
1. Search for linter/formatter configs:
   - `.eslintrc*`, `eslint.config.*`, `.prettierrc*`, `prettier.config.*`
   - `ruff.toml`, `pyproject.toml` `[tool.ruff]`, `.flake8`, `.pylintrc`, `mypy.ini`
   - `rustfmt.toml`, `.rustfmt.toml`
   - `.editorconfig`
   - `biome.json`, `biome.jsonc`
2. Sample 3-5 source files to identify:
   - Naming conventions (variables, functions, classes, files)
   - Import organization patterns
   - Error handling style (try/catch vs Result vs Either)
   - Comment style and density
3. Check directory structure for architecture pattern:
   - `src/controllers/`, `src/models/`, `src/views/` → MVC
   - `src/domain/`, `src/application/`, `src/infrastructure/` → Clean/Hexagonal
   - `src/features/` or `src/modules/` → Feature-based
   - `packages/` or `apps/` → Monorepo
   - Flat `src/` with mixed files → Flat structure
   - **Next.js North Star**: `app/(group)/` route groups per feature OR `features/` alongside `app/`, colocated `components/hooks/actions/` per feature, thin `app/page.tsx` files that import from feature directories, shared `lib/` or `utils/` layer → North Star Architecture

### Output
- Naming convention per entity type
- Architecture pattern with evidence
- Linter/formatter tools and config paths
- Notable patterns (e.g., barrel exports, dependency injection)

## 3. Build/Test/Deploy

### Procedure
1. Extract scripts from package manager manifest:
   - `package.json` → `scripts` object
   - `pyproject.toml` → `[tool.poetry.scripts]` or `[project.scripts]`
   - `Makefile` → targets
   - `justfile` → recipes
2. Search for test configuration:
   - `jest.config.*`, `vitest.config.*`, `cypress.config.*`, `playwright.config.*`
   - `pytest.ini`, `conftest.py`, `pyproject.toml` `[tool.pytest]`
   - `*_test.go`, `*_test.rs`
3. Search for CI/CD:
   - `.github/workflows/*.yml` or `.github/workflows/*.yaml`
   - `.gitlab-ci.yml`
   - `Jenkinsfile`
   - `.circleci/config.yml`
   - `bitbucket-pipelines.yml`
   - `.travis.yml`
4. Search for containers:
   - `Dockerfile`, `docker-compose.yml`, `docker-compose.yaml`
   - `.dockerignore`
5. Search for deploy configs:
   - `vercel.json`, `netlify.toml`, `fly.toml`
   - `serverless.yml`, `sam.yaml`
   - `k8s/`, `kubernetes/`, `helm/`
   - `terraform/`, `*.tf`

### Output
- Install, build, test, lint commands
- Test framework and config path
- CI/CD platform and workflow file paths
- Container setup (yes/no, paths)
- Deploy target and config paths

## 4. Documentation Inventory

### Procedure
1. Check root for: `README.md`, `README.rst`, `CONTRIBUTING.md`, `CHANGELOG.md`, `HISTORY.md`, `LICENSE`
2. Check for docs directory: `docs/`, `doc/`, `documentation/`
3. Search for ADRs: `adr/`, `docs/adr/`, `docs/decisions/`
4. Check for API docs: `openapi.yaml`, `swagger.json`, `docs/api/`
5. Assess README quality: read first 50 lines, check for sections (Installation, Usage, API, Contributing)

### Output
- List of documentation files with paths
- README quality assessment (brief/detailed/comprehensive)
- Docs folder overview
- ADR presence and count

## 5. Git History Analysis

### Commands to Run
```bash
# Recent commits (convention detection)
git -C {target_path} log --oneline -30

# Branch naming patterns
git -C {target_path} branch -r --list 2>/dev/null | head -20

# Most active files (hotspots)
git -C {target_path} log --pretty=format: --name-only -50 | sort | uniq -c | sort -rn | head -15
```

### Procedure
1. Run above commands via execute tool
2. Analyze commit messages for convention:
   - `feat:`, `fix:`, `chore:` → Conventional Commits
   - `[TAG]` prefix → Tag-based
   - `JIRA-123` prefix → Ticket-based
   - Free-form → No convention
3. Check for PR/MR templates: `.github/pull_request_template.md`, `.github/PULL_REQUEST_TEMPLATE/`

### Output
- Commit message convention
- Branch naming pattern
- Most active files (hotspots)
- PR template presence

## 6. Existing Agent Configs

### Files to Search
- `.github/copilot-instructions.md`
- `AGENTS.md` (root and subdirectories)
- `.github/instructions/*.instructions.md`
- `.github/agents/*.agent.md`
- `.github/skills/*/SKILL.md`
- `.github/prompts/*.prompt.md`
- `.github/hooks/*.json`
- `.cursorrules`, `.cursor/rules/`
- `CLAUDE.md`, `.claude/`
- `.windsurfrules`

### Procedure
1. Search for each file pattern above
2. If found, read the file to understand existing customizations
3. Note any conflicts or gaps

### Output
- Complete inventory of existing agent config files
- Summary of what each existing config covers
- Gaps: what's missing from a complete setup

## 7. Security & Policy

### Procedure
1. Check for `.env.example`, `.env.sample` → env var patterns
2. Search for auth-related files: `auth/`, `middleware/auth*`, jwt, oauth
3. Check `.gitignore` for sensitive file patterns
4. Look for security headers, CORS config, rate limiting

### Output
- Env management approach
- Auth mechanism if detected
- Security-relevant patterns

## 8. Complexity Signals

### Procedure
1. Count source files (approximate):
   ```bash
   find {target_path} -type f \( -name "*.ts" -o -name "*.tsx" -o -name "*.js" -o -name "*.jsx" -o -name "*.py" -o -name "*.go" -o -name "*.rs" -o -name "*.java" -o -name "*.rb" -o -name "*.cs" \) -not -path "*/node_modules/*" -not -path "*/.git/*" -not -path "*/vendor/*" -not -path "*/__pycache__/*" -not -path "*/target/*" -not -path "*/dist/*" -not -path "*/build/*" | wc -l
   ```
2. Check for monorepo indicators:
   - `lerna.json`, `nx.json`, `turbo.json`, `pnpm-workspace.yaml`
   - `packages/` directory with multiple sub-packages
   - Multiple `package.json` files at different levels
3. Identify service boundaries:
   - Multiple `Dockerfile` files
   - `services/`, `apps/`, `microservices/` directories
   - Separate build configs per directory

### Output
- Approximate source file count
- Monorepo: yes/no, tool, package count
- Service boundaries if applicable
- Shared libraries if applicable
- Complexity rating: small (<50 files) / medium (50-500) / large (500+)
