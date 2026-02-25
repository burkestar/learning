# Global Agent Standards

## Core Principles (all languages)
- Prefer simple, explicit code over clever abstractions
- No speculative features — only build what's asked
- Always run linting/type checks before declaring done
- Keep changes small and testable
- Use `.env` for configuration and secrets
- Use spaces instead of tabs for code indentation.
- Keep code modules small and self-contained, generally under 1000 lines.

## What NOT to Do
- Don't add dependencies without asking first
- Don't create files in the project root without a reason.
- Don't use print statements for debugging — use the language's preferred logging framework.
- Don't suppress exceptions silently
- Don't hardcode secrets, ports, or paths — use env vars or config

## Git workflow
- Commit often with short one-liner explaining the change, starting with verb like "fixes" or "implements"
- Never force-push to main/master
- Branch naming: `<GITHUB>/<JIRA>-<short name>` where `GITHUB` JIRA is issue key (e.g. DR-123) and short name describes task to aid with memory recall.
- Always check `git diff` before committing
- Do NOT run `git push` — leave that to the human
- Always setup `.gitignore` for a project workspace that covers IDEs (VSCode, Cursor, Jetbrains), language-specific cache and temp paths, and usual things ignored such as `.env`, `tmp/`, etc.

---

## Docker & Local Development

### Image Builds

**Base images**
- Use hardened base images (e.g., `cgr.dev/chainguard/` or `gcr.io/distroless/`)
- Pin to a specific digest or minor version — never `latest`
  - Good: `cgr.dev/chainguard/python:3.12`, `gcr.io/distroless/go:1.22`
  - Bad: `python:latest`, `alpine:latest`

**Multi-stage builds (required)**
- Stage 1 (`builder`): compile/build with full toolchain, statically linked binaries
- Stage 2 (`final`): hardened base + artifact only — no build tools, no package managers
- Copy only the compiled artifact and the minimum runtime dependencies

```dockerfile
# Example pattern
FROM golang:1.22 AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN --mount=type=cache,target=/go/pkg/mod go mod download
COPY . .
RUN --mount=type=cache,target=/root/.cache/go-build \
    CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o server .

FROM gcr.io/distroless/static-debian12 AS final
COPY --from=builder /app/server /server
ENTRYPOINT ["/server"]
```

**Build performance (target: under 1 minute, under 1 GB)**
- Use `--mount=type=cache` for package manager caches:
  - Go: `--mount=type=cache,target=/go/pkg/mod` and `/root/.cache/go-build`
  - Python: `--mount=type=cache,target=/root/.cache/pip`
  - Node: `--mount=type=cache,target=/root/.npm`
- Copy dependency manifests first, then source — maximize layer cache hits
- Use parallel stages where possible (`FROM ... AS stage` runs concurrently with BuildKit)
- Enable BuildKit: `DOCKER_BUILDKIT=1 docker build .` (default in Docker 23+)

**Secret handling**
- Never `ENV` or `ARG` secrets — they appear in image history
- Use secret mounts at build time:
```dockerfile
RUN --mount=type=secret,id=npmrc,target=/root/.npmrc npm install
```

```bash
# Pass from local file or env var — never hardcoded
docker build --secret id=npmrc,src=$HOME/.npmrc .
docker build --secret id=token,env=MY_TOKEN .
```
- Verify no secrets leaked: `docker history <image> --no-trunc`

**`.dockerignore`**
- Mirror `.gitignore` exactly as a baseline, then add:
```
.git
.env*
*.md
```

**Platform targeting**
- Always specify platform explicitly: `docker build --platform linux/amd64 .`
- Default to `linux/amd64` for CI/prod parity

**Signal handling (PID 1)**
- Always use exec form — never shell form:
  - Wrong: `ENTRYPOINT ./server`
  - Right: `ENTRYPOINT ["/server"]`
- If shell preprocessing is needed, end `entrypoint.sh` with `exec "$@"`

**Before committing a Dockerfile**
```bash
hadolint Dockerfile              # lint
docker build -t myapp:dev .      # verify build
docker image ls myapp:dev        # check size < 1GB
trivy image myapp:dev            # vulnerability scan
docker history myapp:dev --no-trunc | grep -i secret  # verify no secret leaks
```

### Local Dev: Tilt + Kubernetes (preferred)

**Use Tilt for all active development.** It builds images, deploys via Helm, and live-reloads on code changes.

**Cluster choice — human decides, Tilt respects kubectl context**
- Docker Desktop (simplest, no install)
- kind: `kind create cluster`
- k3d: `k3d cluster create dev`
- Remote cluster: set context via `kubectl config use-context <name>`
- Tilt reads the active context automatically — do not hardcode cluster config in Tiltfile

**Helm charts**
- Charts live in `charts/<service-name>/`
- Keep templating minimal — prefer simple, readable YAML over clever helpers
- Values files:
  - `charts/<service>/values.yaml` — defaults
  - `charts/<service>/values.dev.yaml` — local dev overrides (image pull policy, replicas, etc.)
- Avoid: complex `_helpers.tpl` logic, deeply nested conditionals, abstracting things that only vary once

**Tiltfile conventions**
```python
# Respect the human's chosen cluster — no hardcoded context
allow_k8s_contexts(['docker-desktop', 'kind-dev', 'k3d-dev', 'staging'])

# Build via Docker, deploy via Helm
docker_build('myapp', '.', 
  cache_from='myapp:cache')

k8s_yaml(helm('./charts/myapp', values=['./charts/myapp/values.dev.yaml']))

k8s_resource('myapp', port_forwards='8080:8080')

# Non-container tasks (migrations, codegen)
local_resource('db-migrate', 'uv run alembic upgrade head',
  deps=['alembic/versions/'])
```

**Workflow**
```bash
tilt up          # start everything
tilt down        # tear down (keeps cluster)
tilt up --stream # debug Tilt itself
```
- Do NOT use `tilt ci` locally — that is for CI pipelines only
- Resource names in Tiltfile must match Helm release names and production manifest names

**Live update**
- Define sync rules per-service — do not sync the entire repo
- Prefer image rebuild over live sync for compiled languages (Go, Rust)
- Use live sync for interpreted languages (Python, TypeScript) to avoid rebuild overhead

### docker-compose (CI and simple shared envs only)

Use docker-compose **only** for:
- CI integration tests against pre-built images
- Sharing a reproducible env with non-engineers who don't have a k8s cluster
- Do not use for active feature development — use Tilt instead
```bash
docker compose up -d      # note: `docker compose` not `docker-compose`
docker compose down -v    # include -v to clean volumes
```

- Always define `healthcheck` on services others depend on
- Use `depends_on: condition: service_healthy` — not bare `depends_on`
- Local overrides in `docker-compose.override.yml` (gitignored)
- Never hardcode env vars in compose files — use `.env` (gitignored)

### What NOT to Do
- Don't use `privileged: true` without explicit human approval
- Don't expose unnecessary ports
- Don't run `docker system prune -a` without warning — it is destructive
- Don't use `RUN apt-get install` in the final stage — only in builder
- Don't hardcode kubectl context in Tiltfile — let the human choose
- Don't add Helm templating just because you can — keep charts readable

---

## Python
**Only apply these rules when working in `.py` files or Python projects.**

### Package Manager
- **Always use `uv`** — never raw `pip`, `pip3`, or `virtualenv`
- Install deps:        `uv add <package>`
- Install dev deps:    `uv add --dev <package>`
- Run scripts:         `uv run python <script.py>`
- Run tools:           `uv run pytest`, `uv run ruff check .`
- Sync environment:    `uv sync`
- Do NOT manually create or activate virtualenvs — uv handles `.venv` automatically
- Use latest available version of python.

### Project Structure
- Use `pyproject.toml` only — no `setup.py`, no `requirements.txt`
- Source layout: `src/<package_name>/`
- Tests: `tests/` mirroring `src/` structure
- Config/secrets: `.env` (never committed)

### Code Style
- **Formatter**: `uv run ruff format .`
- **Linter**: `uv run ruff check . --fix`
- **Types**: `uv run mypy src/`
- Type hints required on all function signatures
- Prefer `pathlib.Path` over `os.path`
- Use `dataclasses` or `pydantic` for structured data
- f-strings only — no `.format()` or `%`
- Prefer `httpx` over `requests`

### Testing
- Framework: `pytest` via `uv run pytest`
- Coverage: `uv run pytest --cov=src --cov-report=term-missing`
- Write tests before or alongside implementation
- Never delete tests — mark skip with reason if needed: `@pytest.mark.skip(reason="...")`
- Test file naming: `test_<module>.py`
- Test behaviors, not implementation. Use test doubles and spies, minimize use of mocks that don't represent real domain objects.

### Before Finishing Any Task
Run these in order and fix all failures before declaring done:
```bash
uv run ruff format .
uv run ruff check . --fix
uv run mypy src/
uv run pytest
```

---

## Go
**Only apply these rules when working in `.go` files or Go projects.**

### Module & Dependency Management
- Initialize: `go mod init <module-path>`
- Add dependency: `go get <package>`
- Clean up unused: `go mod tidy` — run after any add/remove
- Never commit without running `go mod tidy` first
- Do NOT use a `src/` folder — it is discouraged in Go
- `go.mod` and `go.sum` must always be committed together

### Project Layout
Follow standard Go layout — see https://github.com/golang-standards/project-layout:
```
cmd/
    <appname>/
        main.go         ← minimal, delegates to internal/
internal/               ← private code, enforced by Go compiler
    <domain>/
        <domain>.go
        <domain>_test.go
pkg/                    ← public, reusable libraries (only if needed externally)
api/                    ← OpenAPI/protobuf/JSON schema definitions
configs/                ← environment configuration
build/
    package/            ← Dockerfile, packaging scripts
    ci/                 ← CI configuration
deploy/                 ← Helm charts, docker-compose, Terraform
scripts/                ← build/test/deploy scripts, referenced by Makefile
tests/
    testdata/           ← test fixtures, ignored by Go toolchain
```
- `cmd/<name>/main.go` must be minimal — import and delegate to `internal/`
- Keep most logic in `internal/` — it cannot be imported by external projects
- Use `pkg/` only for code explicitly intended for external reuse

### Naming & Code Conventions
- Package names: lowercase, no underscores, no mixedCase (`userauth` not `user_auth`)
- Exported identifiers: UpperCamelCase — visible outside the package
- Unexported identifiers: lowerCamelCase — private to the package
- Multi-word: camelCase only — never snake_case
- Errors returned as values: `value, err := fn()` — always check, never ignore with `_`
- No `init()` functions unless absolutely necessary
- Prefer `context.Context` as the first argument on any function doing I/O

### Build & Run
- Dev (with live reload): `air` — watches files and triggers rebuild on save
  - Config in `.air.toml` at project root
  - Install: `go install github.com/air-verse/air@latest`
- Build binary: `go build -o bin/<name> ./cmd/<name>/`
- Static binary for containers:
  `CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o bin/<name> ./cmd/<name>/`
- Run directly (dev only): `go run ./cmd/<name>/`
- Cross-compile: set `GOOS` and `GOARCH` env vars before `go build`
- Useful env vars:
  - `GOCACHE` — build cache (default `~/.cache/go-build`)
  - `GOMODCACHE` — module cache (default `$GOPATH/pkg/mod`)
  - `go env` — inspect all current values

### Formatting & Linting
- Format: `gofmt -w .` or `goimports -w .` (prefer goimports — also fixes imports)
- Lint: `golangci-lint run`
  - Config in `.golangci.yml` at project root
  - Install: `brew install golangci-lint` or `go install`
- Vet: `go vet ./...` — catches common mistakes, always run before committing
- Do NOT leave unformatted code — `gofmt` is non-negotiable in Go

### Testing
- Run all tests: `go test ./...`
- Run with race detector: `go test -race ./...` — always use in CI
- Run specific test: `go test -run TestFunctionName ./path/to/package`
- With coverage: `go test -cover ./...`
- Assertions and mocks: **Testify** (`github.com/stretchr/testify`)
  - `assert` for non-fatal assertions
  - `require` for fatal assertions (stops test on failure)
  - `mock` for interface mocking
- Test files co-located with source: `something_test.go` next to `something.go`
- Use table-driven tests for multiple input/output scenarios
- Prefer `testdata/` subdirectory for fixture files
- Never use `fmt.Println` for test debugging — use `t.Log` and `t.Logf`

### Web Frameworks (choose one per project)
- **Gin** — preferred for new projects: fast, zero-allocation router, middleware support
  - `github.com/gin-gonic/gin`
- **Chi** — preferred when you want idiomatic, lightweight, composable routing
  - `github.com/go-chi/chi`
- **net/http stdlib** — preferred when you want full control and minimal dependencies
- Do NOT use Gorilla (unmaintained), Beego (too opinionated for services), or Fiber
  unless the project already uses them

### Database
Choose based on project needs — do not mix ORM styles in a single project:
- **GORM** — model-first, most popular, good for CRUD-heavy apps
  - `gorm.io/gorm` + appropriate driver (`gorm.io/driver/postgres`, etc.)
- **bun** — SQL-first, type-safe, fluent API; preferred when SQL control matters
  - `github.com/uptrace/bun`
- **sqlc** — write SQL, generate type-safe Go code; preferred for complex queries
  - `github.com/sqlc-dev/sqlc`
- **pgx** — raw PostgreSQL driver for maximum performance
  - `github.com/jackc/pgx/v5`
- Always use `context.Context` in database calls for cancellation/timeout propagation

### Logging
- **zerolog** — preferred: extremely fast, zero-allocation, chainable API
  - `github.com/rs/zerolog`
- **zap** — alternative: very fast, zero-allocation, more ergonomic sugared API
  - `go.uber.org/zap`
- **log/slog** — use when minimizing dependencies is a priority (stdlib, Go 1.21+)
- NEVER use `fmt.Println` for logging in services — always use structured logging
- Log fields as key-value pairs, not formatted strings
- Use `context.Context` to propagate loggers through call chains

### Concurrency
- Use `errgroup` for managing groups of goroutines with error propagation
  - `golang.org/x/sync/errgroup`
- Always pass `context.Context` to goroutines that do I/O
- Use channels for communication, mutexes for state protection
- Close channels from the sender, never the receiver
- Run `go test -race ./...` to detect data races

### CLI Tools
- Use **Cobra** for any CLI with subcommands: `github.com/spf13/cobra`
- Use **GoDotEnv** for loading `.env` in development: `github.com/joho/godotenv`

### Protobuf / gRPC
- Use **buf** for protobuf management: `github.com/bufbuild/buf`
  - Lint: `buf lint`
  - Generate: `buf generate`
  - Breaking change detection: `buf breaking`
- Use **grpc-go**: `github.com/grpc/grpc-go`

### Auth
- OAuth2 client (external providers): `golang.org/x/oauth2` or `github.com/markbates/goth`
- OAuth2/OIDC server implementation: `github.com/ory/fosite`

### Before Finishing Any Task
Run in this order — all must pass with zero errors:
```bash
goimports -w .
go vet ./...
golangci-lint run
go test -race ./...
go build ./...
```

### What NOT to Do
- Don't ignore errors with `_` — always handle or explicitly propagate them
- Don't use `fmt.Println` for logging in services
- Don't use `init()` functions unless unavoidable
- Don't put logic in `cmd/` — keep main minimal, delegate to `internal/`
- Don't create a `src/` directory
- Don't use `go run` in production or Docker — always build a binary
- Don't use `CGO_ENABLED=1` for container images unless strictly required
- Don't mix database abstraction styles (ORM + raw SQL) in the same service
- Don't forget `go mod tidy` before committing
- Don't push without running the race detector (`go test -race ./...`)

---

## TypeScript
**Only apply these rules when working in `.ts`/`.tsx` files or TypeScript projects.**

### Package Manager: pnpm (always)
- Install deps:      `pnpm add <package>`
- Install dev deps:  `pnpm add -D <package>`
- Run scripts:       `pnpm <script>`
- Run one-off tools: `pnpm dlx <tool>`
- NEVER use npm or yarn

### Runtime & Scripts: bun
- Run TS scripts directly: `bun run script.ts`
- Dev server (if not using Vite): `bun --hot run src/index.ts`
- bun is for execution — pnpm is still the package manager

### Build
- **Frontend / fullstack**: `Vite` — config in `vite.config.ts`
  - Dev server: `pnpm dev` → `vite`
  - Production: `pnpm build` → `vite build`
- **Backend / library / CLI**: `tsup` — config in `tsup.config.ts`
  - Build: `pnpm build` → `tsup`
  - Watch: `pnpm dev` → `tsup --watch`
- NEVER use webpack, rollup directly, or tsc for bundling

### Lint & Format: Biome (replaces ESLint + Prettier)
- Check + fix:   `pnpm biome check --write .`
- Format only:   `pnpm biome format --write .`
- Lint only:     `pnpm biome lint --write .`
- Config lives in `biome.json` at project root
- Do NOT install eslint, prettier, or their plugins unless the project
  already has them and migration hasn't happened yet

### Type Checking: tsc
- Check only (no emit): `pnpm tsc --noEmit`
- Must pass with zero errors before any task is complete
- `strict: true` required in tsconfig — non-negotiable

### Testing: Vitest
- Run:          `pnpm vitest run`
- Watch mode:   `pnpm vitest`
- Coverage:     `pnpm vitest run --coverage`
- Config lives in `vitest.config.ts` or inline in `vite.config.ts`
- Test files:   `*.test.ts` or `*.spec.ts` co-located with source
- Do NOT use Jest unless the project already uses it

### Code Style
- `strict: true` in tsconfig — no exceptions
- No `any` — use `unknown` + type guards
- Prefer `const`, named exports, functional patterns
- `zod` for runtime validation of external data (API responses, env vars)
- ESM only — no CommonJS (`require`, `module.exports`)
- Prefer `satisfies` operator over type assertions where possible

### Before Finishing Any Task
Run in this order — all must pass:
```bash
pnpm biome check --write .
pnpm tsc --noEmit
pnpm vitest run
```

### What NOT to Do
- Don't use `npm` or `yarn` — always `pnpm`
- Don't use `ts-node` — use `bun run` for scripts
- Don't install `eslint` or `prettier` on new projects
- Don't use `any` — ever
- Don't use CommonJS (`require`/`module.exports`)
- Don't use `tsc` to emit — use Vite or tsup
- Don't configure Jest — use Vitest
