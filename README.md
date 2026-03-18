# AI Skills

A collection of portable [Agent Skills](https://agentskills.io) for AI coding assistants.

These skills follow the open [Agent Skills specification](https://agentskills.io/specification) and work with any compatible tool including Claude Code, Cursor, Gemini CLI, VS Code Copilot, OpenHands, Goose, Roo Code, and [many more](https://agentskills.io/home).

## Skills

| Skill | Description |
|-------|-------------|
| [owasp-security-review](owasp-security-review/) | Systematic security review based on OWASP ASVS 5.0 |

## Installation

### Claude Code

```bash
claude install-skill /path/to/ai-skills/owasp-security-review
```

Or copy the skill directory to `~/.claude/skills/`.

### Cursor

Add the skill directory path in Cursor settings under Agent Skills.

### Other tools

Refer to your tool's [Agent Skills documentation](https://agentskills.io/home) for installation instructions. Most tools support pointing to a local directory or cloning from a Git repository.

## Validation

Skills can be validated against the spec using the reference library:

```bash
npx skills-ref validate ./owasp-security-review
```

## Author

Tomi P. Hakala ([@tphakala](https://github.com/tphakala))

## License

Apache-2.0
