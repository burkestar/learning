# Golang

- [Documentation](https://go.dev/doc/)
    - [Tour of Go](https://go.dev/tour/)
    - [How to write Go code](https://go.dev/doc/code)
    - [Go language spec](https://go.dev/ref/spec)
    - [Standard library](https://pkg.go.dev/std)
    - [Effective Go](https://go.dev/doc/effective_go) - writing idiomatic go code
- [Go by example](https://gobyexample.com)
- Cheatsheets: [devhints](https://devhints.io/go), [golang-cheat-sheet](https://github.com/a8m/golang-cheat-sheet)
- [Go interview practice](https://github.com/RezaSi/go-interview-practice)

## Books

- [Let's Go](https://lets-go.alexedwards.net)
- [Learning Go](https://www.amazon.com/Learning-Go-Idiomatic-Real-World-Programming/dp/1098139291/ref=sr_1_5?dib=eyJ2IjoiMSJ9.g7w_cTVkRCbXfYYfB-x4Qi8W6-S1J4wCKgbZwyAz3JbYJ0SZgypSLwea0pjRkPSKiyMz99CR4qkrlbcpd6qZn3usx8LSIAQlCg3PIzfUcby2aAJCjG5SsPmBK8lennI8xUVriAm6PhXGWFwNpQFr_mqlxMLzMmS4NTOkaR0CpOppgkJpxLdhospjzDIyV41QSSwgWEEI1jWUeTiJez_P6k1RzL1VksZl4Ttf9n22IwolNUi4plE-Yjig5CSm8rDCcyBwekrmyjzi_YQRYQXZXK3AafruQbsyktETu-G44GU.vy7CLtyITN1X4u8gwUcXSOfI8FOShyRgqMyUUH_pW-4&dib_tag=se&keywords=golang&qid=1755034148&sr=8-5)
- [Go Programming Language](https://www.amazon.com/Programming-Language-Addison-Wesley-Professional-Computing/dp/0134190440/ref=sr_1_1_sspa?dib=eyJ2IjoiMSJ9.g7w_cTVkRCbXfYYfB-x4Qi8W6-S1J4wCKgbZwyAz3JbYJ0SZgypSLwea0pjRkPSKiyMz99CR4qkrlbcpd6qZn3usx8LSIAQlCg3PIzfUcby2aAJCjG5SsPmBK8lennI8xUVriAm6PhXGWFwNpQFr_mqlxMLzMmS4NTOkaR0CpOppgkJpxLdhospjzDIyV41QSSwgWEEI1jWUeTiJez_P6k1RzL1VksZl4Ttf9n22IwolNUi4plE-Yjig5CSm8rDCcyBwekrmyjzi_YQRYQXZXK3AafruQbsyktETu-G44GU.vy7CLtyITN1X4u8gwUcXSOfI8FOShyRgqMyUUH_pW-4&dib_tag=se&keywords=golang&qid=1755034148&sr=8-1-spons&sp_csd=d2lkZ2V0TmFtZT1zcF9hdGY&psc=1)
- [100 Go mistakes and how to avoid them](https://www.amazon.com/100-Mistakes-How-Avoid-Them/dp/1617299596/ref=sr_1_7?dib=eyJ2IjoiMSJ9.g7w_cTVkRCbXfYYfB-x4Qi8W6-S1J4wCKgbZwyAz3JbYJ0SZgypSLwea0pjRkPSKiyMz99CR4qkrlbcpd6qZn3usx8LSIAQlCg3PIzfUcby2aAJCjG5SsPmBK8lennI8xUVriAm6PhXGWFwNpQFr_mqlxMLzMmS4NTOkaR0CpOppgkJpxLdhospjzDIyV41QSSwgWEEI1jWUeTiJez_P6k1RzL1VksZl4Ttf9n22IwolNUi4plE-Yjig5CSm8rDCcyBwekrmyjzi_YQRYQXZXK3AafruQbsyktETu-G44GU.vy7CLtyITN1X4u8gwUcXSOfI8FOShyRgqMyUUH_pW-4&dib_tag=se&keywords=golang&qid=1755034148&sr=8-7)

## Videos

- [Rob Pike on Go Proverbs](https://www.youtube.com/watch?v=PAAkCSZUG1c)
- [Rob Pike on what we got right and what we got wrong](https://www.youtube.com/watch?v=yE5Tpp2BSGw)
- [Rob Pike on Simplicity is Complicated](https://www.youtube.com/watch?v=rFejpH_tAHM)
- [Rob Pike on Concurrency is Not Parallelism](https://www.youtube.com/watch?v=oV9rvDllKEg)

## Notes

### Design goals

- Simplicity
- Efficiency
- Cross-platform
- Minimal standard lib
- Backwards compatible (stable language without breaking changes)

### Language features

- Strong, static, structural typing
- Compiled
- Garbage collected
- CSP-style concurrency with goroutines and channels for message passing
- Runtime polymorphism via composition and interfaces instead of class inheritance
- Error codes instead of exception handling
- Generics were introduced using parametric polymorphism with type parameters

### Commands

- `go fmt` for formatting code
- `go vet` for static code analysis to detect potential errors
- `go mod init` for creating new module and `go mod tidy` to clean up unused dependencies
- `go get` for managing module dependencies (fetching remote modules)
- `go build` for building go binaries
- `go test` for unit testing
- `go run` for building and executing code (shortcut)
- `go install` for building and installing go modules

### Structure

- **Packages** are a collection of Go source files in the same directory with the same package name, as a way to organize related code into reusable units for modularity and to prevent naming conflicts.
- **Modules** are a collection of one or more Go Packages with a `go.mod` at its root, enabling versioning, dependency management, consistent builds and reproducible environments.  `go.mod` defines the module path used for importing, and dependency requirements.
- Modules produce either a library (importable module) or an executable.

Layout and Naming Conventions:

- `main` package is special and implements `main` function as entrypoint to executable
- Go ignores directories and files starting with `.` or `_`.
- Do not use `src/` folder, its discouraged.
- Package names are lowercase with no under_scores or mixedCase.
- Uppercase identifiers are exported (public) outside the package, whereas lowercase identifiers are private.
- Use camelCase for multi-word identifiers
- Design principles: maintainability, encapsulation, separation of concerns, testability, stability of public interfaces

Common layout:

```text
api/                            - API spec definitions (OpenAPI, JSON schema, protocol definitions)
assets/                         - Miscellaneous assets for the project (images, logos, etc.)
build/                          - 
    package/                    - cloud (AMI), container (Docker), or OS (rpm, deb) package configuration and scripts
    ci/                         - Continuous Integration configuration scripts
cmd/                            - main application entrypoints. Minimal amount of code. Mostly use code from /internal and /pkg.
    foo/                        - directory name should match the name of the executable you want, in this case "foo" command line tool
        main.go                 - command line tool
    server/
        main.go                 - webserver
configs/                        - configuration for different environments
deploy/                         - Configurations and templates for deployment to IaaS/PaaS and container orchestration platforms. Docker-compose, Helm, Terraform, etc.
docs/                           - project documentation
init/                           - system init (systemd) and process manager (supervisord) configs 
internal/                       - private code used by the package not intended to be imported by external projects. Go compiler enforces it. Internal packages can be nested at any level.
    something/
        something.go            - some internal code
        something_test.go       - unit tests for something.go        
pkg/                            - public, reusable code meant to be imported by external projects, shared libraries and components with general purpose functionality
scripts/                        - scripts for building, deploying, testing and maintaining the project. Often referenced by root-level Makefile to keep simple and minimal.
tests/                          - unit and integration tests for the project
    testdata/                   - Data for tests, ignored by Go toolchain
vendor/                         - application dependencies
web/                            - contains web assets (html, css, javascript)
LICENSE                         - project license file
README.md                       - readme to describe the project and provide important links (to docs for how to install and get started)
go.mod                          - defines the Go module and its package dependencies
go.sum                          - cryptographic hash of project and dependencies to provide reproducible builds and verify integrity of third party software packages
```

See [project-layout](https://github.com/golang-standards/project-layout) but note its not official.

### Code conventions

- Error codes returned by functions `value, err := function()`

### Build

- `go build` outputs binary to current directory. Used for creating standalone executable for deployment or local testing. Can be used for cross-compiling to different architectures.
- `go install` outputs compiled binaries to `$GOBIN` or `$GOPATH/bin` (usually `$HOME/go/bin`)
- `go get` retrieves module source code and dependencies into the **module cache** - either `$GOMODCACHE` or `$GOPATH/pkg/mod`.
- `go build` uses a **build cache** to store output and speed up subsequent builds - usually in user cache directory `~/.cache/go-build` (override with `$GOCACHE`)
- **module cache** contains source code, **build cache** contains compiled binaries

### Environment variables

- `$GOPATH` for Go workspace where it can find source code, compiled packages and installed binaries.
- `$GOROOT` for location to Go toolchain and standard library
- `$GOBIN` where binaries are installed
- `$GOOS` and `$GOARCH` for target operating system and architecture when compiling
- `$GOPROXY` configures proxy server for downloading Go modules
- `$GOCACHE` for build cache where compiled binaries and test results are stored
- `$GOMODCACHE` for path to module cache
- `$GOGC` for tuning garbage collector
- Use `go env` to look up the value for these settings

### Tools and Frameworks

- [Awesome Go](https://github.com/avelino/awesome-go) compilation of tools and libraries by category

Editor:

- VS Code or Cursor with Go extension (official from golang)
- GoLand from Jetbrains

Web:

- [net/http](https://pkg.go.dev/net/http) stdlib is recommended by many experienced golang developers. Gives you more control and understanding of how everything is wired together. [Writing web applications](https://go.dev/doc/articles/wiki/) using net/http.
- [Gin](https://gin-gonic.com) - Web framework that is fast, with zero-allocation router, middleware support. Recommended for new projects in golang.
- [Chi](https://github.com/go-chi/chi) - fast, lightweight, idiomatic, composable. Recommended for new projects in golang
- [Fiber](https://gofiber.io) - extremely fast. Routing, middleware, JSON rendering, Template engine, Websockets, Rate limiting.
- Gorilla - advanced routing
- Beego - feature rich, full stack, includes caching and ORM support. Recommended if you want an opinionated full stack framework.

Database:

- [pgx](https://github.com/jackc/pgx) - Postgresql driver that is low-level and high performance
- [GORM](https://gorm.io/index.html) - model-first, full featured, multi-database support, migrations support, production ready. Most popular and recommended.
- [bun](https://github.com/uptrace/bun) - SQL-first, type safe, multi-database support, migrations support, production ready. Fluent-style API for expressing SQL-like queries with Go code.
- [sqlc](https://github.com/sqlc-dev/sqlc) - SQL-first. Write SQL, generate type-safe Golang code to use as your persistence layer.

Logging:

- [log/slog](https://pkg.go.dev/log/slog) stdlib for structured logging, added in go 1.21. Decent performance. Not fully zero allocation. Supports different logging library backends that implement Handler interface (zap, zerolog, etc.)
- [zerolog](https://github.com/rs/zerolog) - extremely fast, very efficient (zero allocation), chainable API
- [zap](https://github.com/uber-go/zap) - very fast, very efficient (zero allocation). More ergonomic "sugared" API or lower-level, more performant core API.

Testing:

- [Testify](https://github.com/stretchr/testify) for assertions and mocks
- [GoConvey](https://smartystreets.github.io/goconvey/) for BDD-style tests with a web UI for results reporting, watch mode to rerun tests on save, IDE integration with notifications.

CLI:

- [Cobra](https://cobra.dev)

Configuration:

- [GoDotEnv](https://github.com/joho/godotenv)

Auth:

- [goth](https://github.com/markbates/goth) with OAuth 2 support for many providers. Client library for working with external Authorization Server.
- [fosite](https://github.com/ory/fosite) security-first OAuth2 and OpenID Connect framework from Ory. Implementation for Authorization Server but also can be used for Resource Server in OAuth to validate tokens and bind to client identities.
- [x/oauth2](https://pkg.go.dev/golang.org/x/oauth2) official, widely used. Client library for working with OAuth providers.

Misc:

- [errgroup](https://pkg.go.dev/golang.org/x/sync/errgroup) for working with groups of coroutines for tasks
- [uuid](https://github.com/google/uuid)
- [grpc-go](https://github.com/grpc/grpc-go) for gRPC
- [buf](https://github.com/bufbuild/buf) for protobuf
- [air](https://github.com/air-verse/air) for live reload to watch files and trigger a rebuild
