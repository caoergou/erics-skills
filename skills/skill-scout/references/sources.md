# Skill Discovery Sources

## Registries & Marketplaces

### skills.sh (Vercel)
- **URL**: https://skills.sh
- **Trending**: https://skills.sh/trending
- **Scale**: 91,500+ SKILL.md entries
- **Signal**: Install counts (anonymous telemetry), trending/hot leaderboards
- **Agents**: 20+ platforms (Claude Code, Cursor, Codex CLI, Gemini CLI, GitHub Copilot, Windsurf, etc.)
- **Notes**: Largest SKILL.md-specific catalog. No MCP support. Community-driven.

### ClawHub
- **URL**: https://clawhub.ai
- **Scale**: 3,200+ skills
- **Signal**: Staff picks, popular downloads, versioned installs
- **Install**: `npx clawhub@latest install {skill-name}`
- **Notes**: "npm for AI agents." Versioned with rollback. Vector search. Was target of ClawHavoc supply chain attack (Jan 2026). No automated security scanning at time of incident.

### Loaditout
- **URL**: https://loaditout.ai
- **Scale**: 21,000+ entries (MCP + SKILL.md unified)
- **Signal**: Security grades, community ratings, automated compatibility testing
- **Install**: `npx loaditout add {user/skill}`
- **Agents**: 12 platforms
- **Notes**: Bridges MCP tools and SKILL.md in one registry. Security grading is a differentiator.

### AgentSkillsHub
- **URL**: https://agentskillshub.dev
- **Scale**: 13,800+ security-scanned skills
- **Signal**: A-F security grades, heat score (adoption + audit), curated collections
- **Notes**: Security-first approach. Every skill graded before listing. OpenClaw leaderboard with 2,993 cleaned skills.

### LobeHub Skills Marketplace
- **URL**: https://lobehub.com/skills
- **Scale**: 100,000+ skills (self-described "world's largest")
- **Install**: `npx -y @lobehub/market-cli skills install {identifier}`
- **Search**: `npx -y @lobehub/market-cli skills search --q "KEYWORD"`
- **Notes**: Massive catalog. Community ratings. Supports multiple agent platforms.

### OpenForge
- **URL**: https://openforgeai.tools
- **Scale**: 2,400+ developers
- **Signal**: Community ratings, $FORGE token economy
- **Install**: `openforge install {skill-name}`
- **Frameworks**: CrewAI, AutoGen, LangGraph, LangChain, MCP, OpenAI Agents
- **Notes**: Token-powered marketplace. Multi-framework. Covers DeFi, DevOps, support, analytics, marketing.

### SkillsMP
- **URL**: https://skillsmp.com
- **Signal**: Smart search, category filtering, quality indicators
- **Notes**: GitHub crawler approach. Read-only discovery.

### SkillsRouter
- **URL**: https://skillsrouter.sh
- **Scale**: 50+ serverless skills
- **Signal**: Built on Agent Skills spec
- **Install**: `npx skills add skillsrouter/skills@{skill-name}`
- **Notes**: Serverless execution — no local GPU needed. 30+ agent platforms.

### MCP-Focused Registries (related ecosystem)

| Registry | URL | Scale | Notes |
|----------|-----|-------|-------|
| Glama | glama.ai | 21,100+ MCP servers | Quality grades, extensive filtering |
| Smithery | smithery.ai | 6,590+ MCP servers | Hosted proxy — zero local install |
| mcp.so | mcp.so | 19,600+ MCP servers | Fast-growing marketplace |
| PulseMCP | pulsemcp.com | ~12,000 | MCP-focused |

---

## GitHub Repositories

### Official & Major

| Repo | Stars | Description |
|------|-------|-------------|
| anthropics/skills | 82,000+ | Official Anthropic reference skills |
| vercel-labs/agent-skills | 21,900+ | Official Vercel skill collection |
| vercel-labs/skills | 7,900+ | The `npx skills` CLI tool itself |
| agentskills/agentskills | 11,700+ | Agent Skills specification & docs |
| numman-ali/openskills | 8,700+ | Universal skills loader |

### Community Collections

| Repo | Stars | Description |
|------|-------|-------------|
| antfu/skills | — | Anthony Fu's curated collection |
| nicepkg/tool-belt | — | Utility skills |
| nicepkg/vibe-create | — | Frontend design skills |
| gohypergiant/agent-skills | — | Enterprise TypeScript/React skills |
| openserv-labs/skills | — | Multi-agent workflow skills |
| tech-leads-club/agent-skills | 1,600+ | Security-validated registry |
| Orchestra-Research/AI-Research-SKILLs | 4,200+ | AI research skills |

### Emerging Standards

- **Cloudflare RFC**: `.well-known/skills/` URI-based discovery for enterprise
- **Ctxly**: Machine-readable directory of agent services (ctxly.com/services.json)

---

## Security Resources

### Key Reports

| Report | Source | Date | Key Finding |
|--------|--------|------|-------------|
| ToxicSkills | Snyk | Feb 2026 | 36.8% of 3,984 skills had security flaws; 76 confirmed malicious |
| ClawHavoc | Multiple | Jan 2026 | Mass malicious upload to ClawHub — credential theft, backdoors |
| CVE-2025-59536 | Check Point | Feb 2026 | MCP consent bypass in Claude Code (CVSS 8.7) |
| CVE-2026-21852 | Check Point | Feb 2026 | API key theft via config hijack (CVSS 5.3) |
| OWASP AST02 | OWASP | 2026 | Supply Chain Compromise — top 10 agentic skill risk |

### Security Tools

| Tool | Purpose | Usage |
|------|---------|-------|
| mcp-scan (Snyk) | Scan skills for malicious patterns | `uvx mcp-scan@latest --skills` |
| skills-ref validate | Spec compliance check | `skills-ref validate ./skill-dir` |
| AgentSkillsHub grades | Pre-computed A-F security scores | Browse agentskillshub.dev |
| Loaditout grades | Security scoring per entry | Browse loaditout.ai |

### Common Attack Patterns

1. **Credential exfiltration**: Encoded `curl` to attacker URL with env vars / API keys
2. **Typosquatting**: `yutube-dl-core` instead of legitimate package
3. **Dependency confusion**: Malicious nested dependency, not the top-level skill
4. **Config-file hijacking**: Override `ANTHROPIC_BASE_URL` to intercept API traffic
5. **Prompt injection**: Instructions that make the agent ignore safety checks
6. **Persistence**: Modify `.claude/settings.json` or hooks for ongoing access
7. **Mass flooding**: Upload hundreds of skills to crowd out legitimate ones

### Red Flags in SKILL.md

- `curl`, `wget`, `eval`, `exec` to unknown URLs
- Base64-encoded strings
- Requests for `Bash` tool access disproportionate to stated purpose
- Instructions to disable security features or ignore warnings
- Dependencies on obscure/new npm/pip packages
- Fetch-and-execute patterns (download remote content and run it)

---

## Category Taxonomy

Skills span far beyond software development:

| Category | Scope |
|----------|-------|
| **Development** | Code review, testing, CI/CD, framework practices, refactoring, linting |
| **AI / ML** | Model integration, embeddings, RAG, prompt engineering, agent self-improvement |
| **Productivity** | Task management, notes, email, calendar, documents, writing |
| **Web & Browser** | Automation, scraping, screenshots, testing, search |
| **DevOps & Cloud** | Infra, containers, deployment, monitoring, cloud providers |
| **Data & Analytics** | ETL, databases, visualization, data science, SQL |
| **DeFi & Finance** | Trading, arbitrage, portfolio, payments, e-commerce |
| **Marketing & Content** | SEO, social media, copywriting, content generation |
| **Customer Support** | Tickets, chatbots, sentiment, multi-channel |
| **Security & Auth** | Vuln scanning, secrets, access control, supply chain audit |
| **Communication** | Slack, Discord, Telegram, WeChat, Lark/Feishu, email bots |
| **Science & Research** | Academic research, experiments, data collection |
| **Education** | Learning tools, tutoring, quizzes, curriculum |
| **Design & Media** | UI/UX, image gen, video, 3D, presentations |
| **Location & Maps** | Weather, geo, maps, travel |
| **Utility** | File ops, storage, search, system tools |
| **Blockchain & Web3** | Smart contracts, token management, DeFi automation |
| **Healthcare** | Medical data, patient tools, compliance |
| **Legal & Compliance** | Contract review, regulatory, policy |
| **Gaming & Entertainment** | Game dev, interactive content, media |
