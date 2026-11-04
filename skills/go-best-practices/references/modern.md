# Modern Go by version

Use a feature only when the `go` directive in `go.mod` is at or above its version, and only in code you are already changing. Newer versions: read `https://go.dev/doc/go1.N`.

| Go | Use | Replaces |
|---|---|---|
| 1.18 | Generics for algorithms over many types; `any` | `interface{}` |
| 1.19 | `atomic.Int64`, `atomic.Bool`, `atomic.Pointer[T]` | `atomic.AddInt64(&x, 1)` on raw ints |
| 1.20 | `errors.Join`; multiple `%w` in one `fmt.Errorf`; `context.WithCancelCause` | hand-rolled multi-error types |
| 1.21 | `min`, `max`, `clear`; `slices`, `maps`, `cmp`; `log/slog`; `sync.OnceValue`; `context.AfterFunc` | `sort.Slice`, manual contains/index loops, third-party structured loggers |
| 1.22 | Per-iteration loop variables; `for i := range n`; `http.ServeMux` patterns (`"GET /items/{id}"`, `r.PathValue`); `math/rand/v2` | `tt := tt` copies, routers used only for method or path matching |
| 1.23 | Range-over-func iterators (`iter.Seq`); `slices.Collect`, `slices.Sorted(maps.Keys(m))` | callback-style visitors, collect-then-sort boilerplate |
| 1.24 | `t.Context()`; `for b.Loop()`; `omitzero` JSON tag; `strings.SplitSeq`; `os.Root`; `tool` directive in `go.mod` | `context.Background()` in tests, `b.N` loops, `tools.go` imports |
| 1.25 | `sync.WaitGroup.Go`; `testing/synctest` for time-dependent tests | `wg.Add(1)` + `defer wg.Done()`, real sleeps in tests |
| 1.26 | `new(expr)`; `errors.AsType[T]`; `go fix` runs the modernizers | `v := x; &v` helpers, `var t *T; errors.As(err, &t)` |
| 1.27 | Generic methods; `encoding/json/v2`; `uuid` package | package-level generic helpers tied to one type, third-party UUID modules |
