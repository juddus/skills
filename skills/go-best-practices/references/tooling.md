# Go verification

Run these after changing Go code, from the module root. Fix what they report in the code you touched; report pre-existing findings elsewhere instead of fixing them.

1. **Format:** `gofmt -l .` prints nothing. Use `goimports` instead if the repo does.
2. **Build and vet:** `go build ./...` and `go vet ./...`.
3. **Lint:** see the lint policy below.
4. **Test:** `go test -race ./...`. Add `-run` to narrow the loop while iterating; finish on the full run.
5. **Modules:** after dependency changes, `go mod tidy -diff` (Go 1.23+) prints nothing.
6. **Modernize:** with Go 1.26+, `go fix -diff ./...` lists rewrites. Apply only those inside touched code.
7. **Vulnerabilities:** after dependency changes, run `govulncheck ./...` if it is installed.

Prefer the repo's own task runner (`make`, `just`, `task`, CI workflow steps) when it wraps these commands, so local runs match CI.

## Lint policy

1. **Project linter first.** Look for `.golangci.yml`, `.golangci.yaml`, `.golangci.toml`, `.golangci.json`, `staticcheck.conf`, a lint target in `Makefile` / `justfile` / `Taskfile.yml`, a lint step in `.github/workflows/`, or a `tool` directive in `go.mod`. Run it exactly as the project does.
2. **Fallback.** No project linter and `golangci-lint` on `PATH`: run it with this skill's opinionated config, leaving the repo untouched:

   ```sh
   golangci-lint run --config <skill-dir>/references/golangci.yml ./...
   ```

   `<skill-dir>` is this skill's base directory. Treat findings in untouched code as context, not work.
3. **None available.** Say so in your reply: the project has no linter configured and `golangci-lint` is not installed, so only `go vet` ran. Suggest adding a `.golangci.yml`; leave adding it to the user.
