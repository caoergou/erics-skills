# Eric's Skills

A curated collection of AI agent skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and other AI coding assistants.

## Available Skills

| Skill | Description | Install |
|-------|-------------|---------|
| [code-review](skills/code-review/) | Comprehensive code review combining Clean Code principles, SOLID analysis, linter checks, security scan, and senior engineer expertise | `npx skills add caoergou/erics-skills --skill code-review` |

## Installation

Install a specific skill using [skills CLI](https://github.com/vercel-labs/skills):

```bash
# Install a skill globally for Claude Code
npx skills add caoergou/erics-skills --skill <skill-name> -g -a claude-code

# Install to current project only
npx skills add caoergou/erics-skills --skill <skill-name>

# List all available skills in this repo
npx skills add caoergou/erics-skills --list
```

## Contributing

Contributions are welcome! Each skill lives in its own directory under `skills/` and must contain at minimum a `SKILL.md` with proper frontmatter:

```
skills/
└── your-skill/
    ├── SKILL.md          # Required: skill definition with name + description frontmatter
    ├── README.md         # Recommended: usage docs
    ├── agents/
    │   └── agent.yaml    # Optional: agent interface config
    └── references/       # Optional: supporting reference docs
```

## License

MIT
