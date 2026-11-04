# Go patterns

Each pair shows the shape to reach for. Adapt to the surrounding code.

## Errors: handle once, wrap with context

```go
// Avoid: logs and returns, so the error is reported twice.
cfg, err := loadConfig(path)
if err != nil {
	log.Printf("failed to load config: %v", err)
	return err
}

// Prefer: add context and return; the top-level caller logs once.
cfg, err := loadConfig(path)
if err != nil {
	return fmt.Errorf("load config %s: %w", path, err)
}
```

Branch on causes with `errors.Is(err, fs.ErrNotExist)` or `errors.As(err, &pathErr)`, never on `err.Error()` text.

## Early return

```go
// Avoid
func (s *Store) Get(id string) (*Item, error) {
	if id != "" {
		item, ok := s.items[id]
		if ok {
			return item, nil
		} else {
			return nil, ErrNotFound
		}
	}
	return nil, errors.New("empty id")
}

// Prefer
func (s *Store) Get(id string) (*Item, error) {
	if id == "" {
		return nil, errors.New("empty id")
	}
	item, ok := s.items[id]
	if !ok {
		return nil, ErrNotFound
	}
	return item, nil
}
```

## Interfaces belong to the consumer

```go
// Avoid: producer exports an interface for its only implementation.
package store

type UserStore interface {
	Get(ctx context.Context, id string) (*User, error)
	Put(ctx context.Context, u *User) error
	Delete(ctx context.Context, id string) error
}

// Prefer: producer returns a concrete type; the consumer declares what it uses.
package store

func NewPostgres(db *sql.DB) *Postgres { return &Postgres{db: db} }

package billing

type userGetter interface {
	Get(ctx context.Context, id string) (*store.User, error)
}

type Service struct{ users userGetter }
```

## Explicit dependencies

```go
// Avoid: hidden global set up in init().
var db *sql.DB

func init() { db, _ = sql.Open("postgres", os.Getenv("DSN")) }

// Prefer: wire in main, pass through constructors.
func main() {
	db, err := sql.Open("postgres", os.Getenv("DSN"))
	if err != nil {
		log.Fatal(err)
	}
	svc := billing.NewService(store.NewPostgres(db), slog.Default())
	// ...
}
```

## Goroutines have an owner

```go
// Avoid: fire-and-forget; errors and panics vanish, nothing waits.
for _, u := range urls {
	go fetch(u)
}

// Prefer: the caller waits and gets the first error.
g, ctx := errgroup.WithContext(ctx)
g.SetLimit(8)
for _, u := range urls {
	g.Go(func() error { return fetch(ctx, u) })
}
if err := g.Wait(); err != nil {
	return fmt.Errorf("fetch: %w", err)
}
```

With Go 1.25+ and no error to collect, `wg.Go(func() { ... })` replaces `wg.Add(1)` plus `defer wg.Done()`.

## Table-driven tests with testify

```go
func TestParseSize(t *testing.T) {
	tests := []struct {
		name    string
		in      string
		want    int64
		wantErr error
	}{
		{name: "bytes", in: "512", want: 512},
		{name: "kilobytes", in: "2K", want: 2048},
		{name: "empty", in: "", wantErr: ErrEmpty},
	}
	for _, tt := range tests {
		t.Run(tt.name, func(t *testing.T) {
			got, err := ParseSize(tt.in)
			if tt.wantErr != nil {
				require.ErrorIs(t, err, tt.wantErr)
				return
			}
			require.NoError(t, err)
			assert.Equal(t, tt.want, got)
		})
	}
}
```

Fakes are small structs satisfying the consumer's interface:

```go
type fakeUsers map[string]*store.User

func (f fakeUsers) Get(_ context.Context, id string) (*store.User, error) {
	u, ok := f[id]
	if !ok {
		return nil, store.ErrNotFound
	}
	return u, nil
}
```

## Traps

```go
// Typed nil: a nil *MyErr inside an error interface is non-nil.
func check() error {
	var e *MyErr
	return e // err != nil is true
}
// Fix: return nil explicitly on the success path.

// Append aliasing: both slices share a backing array.
base := make([]int, 3, 10)
a := append(base, 1)
b := append(base, 2) // overwrites a[3]
// Fix: copy first, e.g. a := append(slices.Clone(base), 1).

// Nil map write panics.
var m map[string]int
m["x"] = 1 // panic
// Fix: m := map[string]int{} or make(map[string]int).

// Defer in a loop holds every file open until the function returns.
for _, p := range paths {
	f, _ := os.Open(p)
	defer f.Close()
}
// Fix: move the body into a function so each defer runs per iteration.
```

## HTTP timeouts

```go
// Avoid: http.DefaultClient and http.ListenAndServe have no timeouts.
client := &http.Client{Timeout: 10 * time.Second}

srv := &http.Server{
	Addr:              ":8080",
	Handler:           mux,
	ReadHeaderTimeout: 5 * time.Second,
	ReadTimeout:       30 * time.Second,
	WriteTimeout:      30 * time.Second,
	IdleTimeout:       2 * time.Minute,
}
```
