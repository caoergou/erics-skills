---
name: skill-scout
description: "Periodic ecosystem radar for AI agent skills. Scans skills.sh, ClawHub, Loaditout, AgentSkillsHub, LobeHub, OpenForge, GitHub and more — produces a shareable digest with security assessments for team evaluation and ecosystem awareness."
---

# Skill Scout

A **periodic ecosystem radar** for AI agent skills. Produces a shareable, newsletter-style digest that answers two questions:

1. **What's worth adopting?** — Trending skills across all major registries, with security vetting, for team evaluation and selection.
2. **Where is the ecosystem going?** — Platform growth, new categories, security landscape, emerging patterns.

The output is designed to be **directly shareable** — paste into Slack, Lark, a team wiki, or a blog post.

> **Scope**: This skill is for discovery and curation, not for searching by specific requirements. For that, use `find-skills`.

---

## Workflow

### Step 1: Gather — Multi-Registry Scan

Fetch from all major sources **in parallel**. Each registry surfaces different signals:

| Source | What to Fetch | Key Signal |
|--------|--------------|------------|
| **skills.sh** | `/trending` page | Install counts, hot/trending boards (91K+ skills) |
| **ClawHub** | Homepage + popular | Staff picks, download counts (3.2K+ skills) |
| **AgentSkillsHub** | Leaderboard | A-F security grades, heat score (13.8K+ skills) |
| **Loaditout** | Browse page | Security grades, MCP + SKILL.md unified (21K+ entries) |
| **LobeHub** | Skills marketplace | Community ratings (100K+ skills) |
| **OpenForge** | Marketplace | Multi-framework coverage, DeFi/non-coding categories |
| **GitHub** | Trending repos with SKILL.md | Stars, forks, publisher reputation |
| **Web** | Blog posts, curated lists, announcements | Qualitative signal, community buzz |

**How**:
- Registry pages: `WebFetch` each URL
- Web signal: `mcp__exa__web_search_exa` — e.g. `"AI agent skills trending new noteworthy {current_year}"`
- GitHub: `mcp__exa__web_search_exa` — e.g. `"SKILL.md agent skill site:github.com"`

Also monitor **well-known publishers** for new releases:

| Publisher | Repo | Known For |
|-----------|------|-----------|
| anthropics | skills | Official reference (82K+ stars) |
| vercel-labs | agent-skills | Official Vercel collection (21K+ stars) |
| antfu | skills | Community-curated |
| nicepkg | tool-belt, vibe-create | Utility & frontend |
| larksuite | lark-skills | Lark/Feishu integrations |
| tech-leads-club | agent-skills | Security-validated |
| gohypergiant | agent-skills | Enterprise-grade |
| openserv-labs | skills | Multi-agent workflows |

### Step 2: Assess — Security Vetting

**Every skill in the digest must carry a trust signal.** This is non-negotiable.

#### Why

- Snyk ToxicSkills (Feb 2026): **36.8%** of 3,984 skills had security flaws; **76 confirmed malicious**.
- ClawHavoc: mass malicious upload to ClawHub — credential theft, backdoors, exfiltration.
- OWASP AST02: Supply Chain Compromise is a top-10 agentic skill risk.
- Skills have **full agent access** — filesystem, shell, credentials, network.

#### Trust Tiers

| Tier | Label | Criteria |
|------|-------|----------|
| **S** | Official | From Anthropic, Vercel, or platform vendor; audited |
| **A** | High Trust | Known publisher, high adoption, security grade A-B, transparent code |
| **B** | Moderate | Established publisher, decent adoption, no red flags |
| **C** | Caution | New/low-adoption, ungraded — review SKILL.md before installing |
| **D** | Risky | Unknown publisher, suspicious patterns — **do not recommend** |

#### What to Check

- **Publisher**: Known org? Account age? Other repos?
- **Registry grade**: A-F from AgentSkillsHub or Loaditout?
- **Code**: Any `curl`/`wget`/`eval`/`exec` to unknown URLs? Base64? Encoded payloads?
- **Dependencies**: Installs obscure npm/pip packages?
- **Permissions**: Requests Bash/network/file access proportional to its purpose?
- **Adoption**: Real install counts, stars, community feedback?

> **Rule**: Never recommend tier D. Always warn on tier C. Quick Install section only includes tier A+.

### Step 3: Categorize

Use broad categories — skills are **not limited to coding**:

Development | AI/ML | Productivity | Web & Browser | DevOps & Cloud | Data & Analytics | DeFi & Finance | Marketing & Content | Customer Support | Security | Communication | Science & Research | Education | Design & Media | Blockchain & Web3 | Utility

A skill can span multiple categories. Don't force-fit.

### Step 4: Generate Digest

Output the following structure. This is the **deliverable** — designed to be copy-pasted as-is into Slack, Lark, wiki, or blog.

```markdown
# Skill Scout Digest

> {date} | Scanned: skills.sh, ClawHub, AgentSkillsHub, Loaditout, LobeHub, OpenForge, GitHub

---

## Ecosystem Pulse

{3-5 sentences on the current state: total skills across registries, growth trend, any platform news, notable shifts in categories or adoption patterns, recent security events}

---

## Recommended Skills

Vetted picks for team adoption. Security tier A or above.

### 1. {skill-name}
> {one-line description}

| | |
|---|---|
| Publisher | {org/repo} |
| Category | {categories} |
| Adoption | {installs} installs / {stars} stars |
| Security | **{tier}** — {one-line justification} |
| Agents | Claude Code, Cursor, Codex, ... |
| Install | `{command}` |

{2-3 sentences: what it does, why it's worth adopting, who benefits}

---

(Repeat for 5-8 top picks across different categories)

---

## Rising — Worth Watching

New or fast-growing skills not yet broadly adopted, but showing momentum:

| Skill | Publisher | Category | Adoption | Security | Why Watch |
|-------|-----------|----------|----------|----------|-----------|
| {name} | {org} | {cat} | {n} | {tier} | {one-line reason} |

---

## Landscape

### By Category

| Category | Trending Skill | Registries Active |
|----------|---------------|-------------------|
| Development | {name} | skills.sh, Loaditout |
| AI / ML | {name} | ClawHub, LobeHub |
| DeFi & Finance | {name} | OpenForge |
| Productivity | {name} | LobeHub, skills.sh |
| ... | ... | ... |

### Platform Health

| Registry | Scale | Security Posture | Trend |
|----------|-------|-----------------|-------|
| skills.sh | {n} skills | No built-in grading | {growing/stable/...} |
| ClawHub | {n} skills | Post-ClawHavoc improvements | {observation} |
| AgentSkillsHub | {n} skills | A-F grading on all entries | {observation} |
| Loaditout | {n} entries | Security grades + MCP support | {observation} |
| LobeHub | {n} skills | Community ratings | {observation} |

---

## Security Radar

### Recent Incidents & Advisories
{Any new supply chain issues, malicious skill takedowns, CVEs, or platform policy changes}

### Install Safety Checklist
1. `uvx mcp-scan@latest --skills` — scan before installing
2. Read the SKILL.md — look for shell commands to unknown URLs, base64, encoded payloads
3. Check the publisher — account age, other repos, community presence
4. Pin versions — use commit SHAs, not `@latest`
5. Least privilege — does the skill need the permissions it requests?

---

## Quick Install

Copy-paste to try the top picks (tier A+ only):

\```bash
{install command 1}
{install command 2}
{install command 3}
\```

---

*Generated by [skill-scout](https://github.com/caoergou/erics-skills) — the ecosystem radar for AI agent skills*
```

### Step 5: Follow-up

```markdown
---

## Next

1. **Install** — which skill(s) to set up
2. **Deep dive** — fetch full SKILL.md + security review for a specific skill
3. **Compare** — side-by-side on 2-3 skills
4. **Save** — export this digest as a .md file
5. **Audit** — scan your currently installed skills with mcp-scan
```

---

## Rules

- Use `mcp__exa__web_search_exa` for all web searches (never built-in search)
- Fetch from sources in parallel for speed
- Every skill must have a trust tier — no exceptions
- Never recommend tier D; always warn on tier C
- Don't fabricate data — if a source is down, note the gap
- Cover all domains, not just coding
- Note data freshness — when was it last checked
- For requirement-based search, redirect to `find-skills`
