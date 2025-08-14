# Golang

- [Documentation](https://go.dev/doc/)
    - [Tour of Go](https://go.dev/tour/)
    - [How to write Go code](https://go.dev/doc/code)
    - [Go language spec](https://go.dev/ref/spec)
    - [Standard library](https://pkg.go.dev/std)
    - [Effective Go](https://go.dev/doc/effective_go) - writing idiomatic go code
- [Go by example](https://gobyexample.com)
- Cheatsheets: [devhints](https://devhints.io/go), [golang-cheat-sheet](https://github.com/a8m/golang-cheat-sheet)


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

## Tools

- VS Code with Go extension (official from golang)
- [Awesome Go](https://github.com/avelino/awesome-go) compilation of tools and libraries by category

## Notes

Design goals:

- Simplicity
- Efficiency
- Cross-platform
- Minimal standard lib
- Backwards compatible (stable language without breaking changes)

Language features:

- Strong, static, structural typing
- Compiled
- Garbage collected
- CSP-style concurrency with goroutines and channels for message passing
- Runtime polymorphism via composition and interfaces instead of class inheritance
- Error codes instead of exception handling
- Generics were introduced using parametric polymorphism with type parameters

Commands:

- `go fmt` for formatting code
- `go vet` for static code analysis to detect potential errors
- `go mod init` for creating new module and `go mod tidy` to clean up unused dependencies
- `go get` for managing module dependencies (fetching remote modules)
- `go build` for building go binaries
- `go test` for unit testing
- `go run` for building and executing code (shortcut)
- `go install` for building and installing go modules

Structure:

- **Packages** are a collection of Go source files in the same directory with the same package name, as a way to organize related code into reusable units for modularity and to prevent naming conflicts.
- **Modules** are a collection of one or more Go Packages with a `go.mod` at its root, enabling versioning, dependency management, consistent builds and reproducible environments.  `go.mod` defines the module path used for importing, and dependency requirements.
- Modules produce either a library (importable module) or an executable.
- `main` package is special and implements `main` function as entrypoint to executable

Build:

- `go build` outputs binary to current directory. Used for creating standalone executable for deployment or local testing. Can be used for cross-compiling to different architectures.
- `go install` outputs compiled binaries to `$GOBIN` or `$GOPATH/bin` (usually `$HOME/go/bin`)
- `go get` retrieves module source code and dependencies into the **module cache** - either `$GOMODCACHE` or `$GOPATH/pkg/mod`.
- `go build` uses a **build cache** to store output and speed up subsequent builds - usually in user cache directory `~/.cache/go-build` (override with `$GOCACHE`)
- **module cache** contains source code, **build cache** contains compiled binaries

Environment variables:

- `$GOPATH` for Go workspace where it can find source code, compiled packages and installed binaries.
- `$GOROOT` for location to Go toolchain and standard library
- `$GOBIN` where binaries are installed
- `$GOOS` and `$GOARCH` for target operating system and architecture when compiling
- `$GOPROXY` configures proxy server for downloading Go modules
- `$GOCACHE` for build cache where compiled binaries and test results are stored
- `$GOMODCACHE` for path to module cache
- `$GOGC` for tuning garbage collector
- Use `go env` to look up the value for these settings

