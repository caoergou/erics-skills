# Eric's Skills

A curated collection of AI agent skills for [Claude Code](https://docs.anthropic.com/en/docs/claude-code) and other AI coding assistants.

## Available Skills

| Skill | Description | Install |
|-------|-------------|---------|
| [code-review](skills/code-review/) | Comprehensive code review: Clean Code + SOLID + linter + security + senior engineer expertise | `npx skills add caoergou/erics-skills --skill code-review` |

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

---

# Eric's Skills（中文）

为 [Claude Code](https://docs.anthropic.com/en/docs/claude-code) 及其他 AI 编程助手精心整理的技能合集。

## 可用技能

| 技能 | 描述 | 安装 |
|------|------|------|
| [code-review](skills/code-review/) | 综合代码审查：Clean Code 原则 + SOLID 分析 + Linter 检查 + 安全扫描 + 资深工程师视角 | `npx skills add caoergou/erics-skills --skill code-review` |

## 安装

使用 [skills CLI](https://github.com/vercel-labs/skills) 安装指定技能：

```bash
# 全局安装技能到 Claude Code
npx skills add caoergou/erics-skills --skill <skill-name> -g -a claude-code

# 仅安装到当前项目
npx skills add caoergou/erics-skills --skill <skill-name>

# 列出本仓库中所有可用技能
npx skills add caoergou/erics-skills --list
```

## 贡献

欢迎贡献！每个技能位于 `skills/` 下的独立目录中，至少需要包含一个带有正确 frontmatter 的 `SKILL.md` 文件：

```
skills/
└── your-skill/
    ├── SKILL.md          # 必需：技能定义（含 name + description frontmatter）
    ├── README.md         # 推荐：使用文档
    ├── agents/
    │   └── agent.yaml    # 可选：Agent 接口配置
    └── references/       # 可选：参考资料
```

## 许可证

MIT
