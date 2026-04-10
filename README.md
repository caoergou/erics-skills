# Eric's Skills

A curated collection of AI agent skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and other AI coding assistants.

为 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 及其他 AI 编程助手精心整理的技能合集。

## Available Skills / 可用技能

| Skill / 技能 | Description / 描述 | Install / 安装 |
|-------|-------------|---------|
| [code-review](skills/code-review/) | Comprehensive code review: Clean Code + SOLID + linter + security + senior engineer expertise | `npx skills add caoergou/erics-skills --skill code-review` |

## Installation / 安装

Install a specific skill using [skills CLI](https://github.com/vercel-labs/skills):

使用 [skills CLI](https://github.com/vercel-labs/skills) 安装指定技能：

```bash
# Install a skill globally for Claude Code
# 全局安装技能到 Claude Code
npx skills add caoergou/erics-skills --skill <skill-name> -g -a claude-code

# Install to current project only
# 仅安装到当前项目
npx skills add caoergou/erics-skills --skill <skill-name>

# List all available skills in this repo
# 列出本仓库中所有可用技能
npx skills add caoergou/erics-skills --list
```

## Contributing / 贡献

Contributions are welcome! Each skill lives in its own directory under `skills/` and must contain at minimum a `SKILL.md` with proper frontmatter.

欢迎贡献！每个技能位于 `skills/` 下的独立目录中，至少需要包含一个带有正确 frontmatter 的 `SKILL.md` 文件。

```
skills/
└── your-skill/
    ├── SKILL.md          # Required / 必需：技能定义（含 name + description frontmatter）
    ├── README.md         # Recommended / 推荐：使用文档
    ├── agents/
    │   └── agent.yaml    # Optional / 可选：Agent 接口配置
    └── references/       # Optional / 可选：参考资料
```

## License / 许可证

MIT
