# Go best practices

Opinionated guidance for reading, writing, and reviewing Go code. The agent
entry point is [SKILL.md](SKILL.md).

## Contents

- [Patterns](references/patterns.md) shows examples of the recommended Go idioms.
- [Modern Go by version](references/modern.md) lists version-specific features.
- [Verification](references/tooling.md) describes formatting, linting, and tests.
- [Fallback linter configuration](references/golangci.yml) applies only when a
  project has no linter setup. The project's own conventions and tools take
  priority.

## Development

Edit the skill and its references together. Keep relative links valid and check
version-specific Go guidance against upstream documentation.

The guidance is distilled from Go Code Review Comments, Effective Go, the Google
Go Style Guide, and Go release notes. The original source documents are not
bundled here.
