# Source Tiers Reference

> This file defines the quality tiers for all data sources used by ai-digest, plus Nitter fallback configuration and KOL tracking lists.

---

## Source Quality Tiers

### Tier 1 — Highest Quality (5 points)

Sources with editorial oversight, verified authority, or strong community validation.

- **Anthropic/OpenAI official blogs** — Authoritative announcements, policy positions
- **Hacker News top posts (>200 upvotes)** — Strong community validation
- **Simon Willison's Blog** — Production-grade, thoroughly vetted insights
- **Verified AI KOL on X/Twitter** — Confirmed identity, professional authority
  - Only counts as Tier 1 if the account is verified and the content is an original announcement or analysis
  - Speculation without evidence is Tier 3 even from a KOL

### Tier 2 — Good Quality (3 points)

Sources with community curation, editorial selection, or technical depth.

- **GitHub Trending** — Star velocity indicates genuine interest
- **Reddit Hot posts** — Community-upvoted content (r/LocalLLaMA, r/MachineLearning, r/AI_Agents)
- **Product Hunt** — New product signals, community-validated
- **Latent Space** — Engineering-focused podcast with depth
- **Ben's Bites** — Curated AI product news
- **TLDR AI** — Broad but curated coverage
- **The Rundown AI** — News with explanation context
- **Last Week in AI** — Comprehensive weekly summary
- **AI Brews** — Concise founder/engineer roun dup
- **Import AI** — Research and policy focus
- **OpenCLI-sourced X/Twitter data** — Direct data from platform API

### Tier 3 — Lower Confidence (1 point)

Sources that are useful but less reliable or harder to verify.

- **General news aggregators** — No domain expertise filter
- **Random blog posts** — No editorial oversight
- **Nitter RSS** — Less reliable than OpenCLI-sourced X data
- **Unverified X/Twitter posts** — Speculation without evidence, even from notable accounts
- **HuggingFace Papers** (trending but unreviewed) — High volume, variable quality

---

## X/Twitter Content Processing Rules

- X posts are **Tier 2** quality source by default (comparable to Reddit)
- X breaking announcements from verified KOLs can be **Tier 1** (comparable to official blog posts)
- X speculation without verification is **Tier 3**
- Always try to find the **original source link** (YouTube video, blog post, paper) rather than linking to an X post alone
- If a topic first appeared on X and then spread to HN/Reddit/newsletters, cite the X post as cross-validation

---

## KOL Tracking Lists

Discover actual handles at runtime via `mcp__exa__web_search_exa` searching "top AI influencers X Twitter 2026". Reference categories:

### AI Founders & Researchers
@AndrewYNg, @ylecun, @SamAltman, @AnthropicAI, @OpenAI

### AI Engineering & Agents
@swyx, @LangChainAI, @CrewAIInc

### Developer Tools
@vercel, @supabase, @railshp

### AI Commentary
@karpathy, @Ethan_Mollick, @emaborell99

> **Note**: Always verify handles at runtime. Accounts get suspended, renamed, or go inactive.

---

## Nitter RSS Fallback

Only use when OpenCLI browser commands fail (exit code 69 or 77).

**Instance priority** (try in order):

| Priority | Instance | Uptime |
|----------|----------|--------|
| 1 | xcancel.com | ~97% |
| 2 | nitter.net | ~94% |
| 3 | nitter.poast.org | ~86% |

**RSS URL format**: `https://{nitter-instance}/{twitter_username}/rss`

**Limitations**:
- No search functionality — only individual user timelines
- Less reliable than OpenCLI-sourced data
- Use `fetch_fetch` or `webfetch` to read RSS content

---

## Platform → OpenCLI Command Mapping

| Source | OpenCLI Command | Fallback |
|--------|----------------|----------|
| Hacker News | `opencli hackernews top/best/new --limit N -f json` | HN Firebase API |
| X / Twitter | `opencli twitter trending/search/timeline -f json` | Nitter RSS |
| Reddit | `opencli reddit hot/subreddit --limit N -f json` | exa search |
| GitHub Trending | `opencli github trending --language python --since weekly -f json` | exa search |
| Product Hunt | No adapter — use exa search only | exa search only |