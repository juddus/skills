# Agent skills

Reusable skills for coding agents. Each skill is self-contained, with a
`SKILL.md` entry point and any supporting references beside it.

## Available skills

| Skill | Purpose |
| --- | --- |
| [go-best-practices](skills/go-best-practices/README.md) | Opinionated guidance for reading, writing, and reviewing Go code, with examples, version-aware features, verification steps, and a fallback golangci-lint configuration. |

## Installation

1. Clone this repository:

   ```sh
   git clone https://github.com/juddus/skills.git
   ```

2. Copy the directory of the skill you want from `skills/` into your agent's
   skill directory. Keep the entire directory, including `SKILL.md` and any
   supporting files.
3. Follow your agent's documentation for the installation location and how to
   enable or load skills.

## License

[MIT](LICENSE).
