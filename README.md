# Agent skills

Reusable skills for coding agents. Each skill is self-contained, with a
`SKILL.md` entry point and any supporting references beside it.

## Available skills

| Skill | Purpose |
| --- | --- |
| [go-best-practices](skills/go-best-practices/SKILL.md) | Opinionated guidance for reading, writing, and reviewing Go code, with examples, version-aware features, verification steps, and a fallback golangci-lint configuration. |

## Installation

Copy `skills/go-best-practices` into your agent's skill directory, preserving
the `references/` directory. The skill does not require OpenCode or Nix.

For OpenCode, install it at
`~/.config/opencode/skills/go-best-practices/`. To develop against this checkout
instead of a copy, create a symlink at that location. Do not replace an existing
installation without removing it through the tool that manages it.

Nix consumers can use this repository as a raw source input:

```nix
inputs.juddus-skills = {
  url = "github:juddus/skills";
  flake = false;
};
```

The skill is available at
`"${inputs.juddus-skills}/skills/go-best-practices"`. Commit the generated
`flake.lock` to pin the source. While developing, use
`--override-input juddus-skills path:/absolute/path/to/skills --no-write-lock-file`.

## Development

Edit the skill and its references together. Keep relative links valid and check
version-specific Go guidance against the linked upstream documentation. The
fallback linter configuration applies only when a project has no linter setup;
the project's own conventions and tools take priority.

The Go guidance is distilled from Go Code Review Comments, Effective Go, the
Google Go Style Guide, and Go release notes. The original source documents are
not bundled here.

## License

[MIT](LICENSE).
