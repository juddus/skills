---
name: go-best-practices
description: Go best practices. Use when reading, writing, or reviewing any .go file.
---

# Go best practices

Consistency with the surrounding code outranks this list: match its patterns, libraries, and Go version. Read the `go` directive in `go.mod` before using a newer language or library feature; the version table is in `references/modern.md`. Modernize only the lines you touch.

Plain first: concrete types, plain functions, and `for` loops. Add generics, interfaces, goroutines, or option structs when a second real caller needs them, not before.

| Rule | Summary |
|---|---|
| Names | Short in small scopes, descriptive in wide ones. Initialisms keep one case (`userID`, `HTTPClient`). Getters drop `Get` (`Owner()`). Names skip the package (`http.Server`, not `http.HTTPServer`). |
| Packages | Short lowercase nouns named for what they provide; `util`, `common`, and `helpers` are buckets, not names. Group by domain, not layer. Hide non-API code in `internal/`. |
| Doc comments | Every exported name has a full-sentence comment starting with the name. Comments explain why; the code shows what. |
| Errors | Check every error at the call site and handle it once: log it or return it. Wrap with `fmt.Errorf("load config: %w", err)` when context helps; use `%v` to keep the cause out of your API. Compare with `errors.Is` / `errors.As`. Lowercase messages, no trailing punctuation. |
| Error types | Export sentinel `ErrX` or type `XError` only when callers branch on it. |
| Panics | Panic only on programmer bugs and `Must` helpers at init. Everything else returns an error. |
| Control flow | Happy path down the left margin: handle errors and edge cases first and return early. No `else` after `return`. |
| Interfaces | Define them in the consuming package, as small as the consumer needs. Accept interfaces, return concrete types. |
| Zero values | Make the zero value useful. Use `var s []T` for empty slices; add a constructor only when the type has invariants. |
| Dependencies | Pass them in through constructor parameters or struct fields. Keep `init()` and package-level mutable state out. |
| Context | `ctx context.Context` is the first parameter, passed down, never stored in a struct. Create root contexts only in `main`, request handlers, and tests (`t.Context()`). |
| Concurrency | Synchronous APIs by default; the caller chooses concurrency. Every goroutine has an owner who waits for it (`errgroup`, `sync.WaitGroup`). Mutex for shared state, channels for handing off ownership. |
| Resources | `defer Close()` right after a successful open. Set timeouts on `http.Client` and `http.Server`. Close `resp.Body`. |
| Logging | `log/slog` with key-value attributes. Libraries take a logger; they do not log to globals. |
| Tests | Table-driven with `t.Run`. Assert with testify: `require` for preconditions, `assert` for checks. Test behaviour through the public API (`package foo_test`). Hand-written fakes over generated mocks. |
| Traps | Typed nil in an interface, `append` aliasing a shared backing array, writes to a nil map, unsynchronised map access, integer conversion overflow, `defer` inside a loop. |

Examples: `references/patterns.md`. Verification and linting: `references/tooling.md`.

Rules distilled from Go Code Review Comments, Effective Go, the Google Go Style Guide, and the Go release notes.
