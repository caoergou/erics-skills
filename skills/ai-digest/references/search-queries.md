# Search Queries Reference

> This file contains all search query templates used by ai-digest across the three-layer discovery workflow.

---

## Layer 1 — Curated Discovery Queries

Used with `mcp__exa__web_search_exa` to discover topics from curated newsletters and aggregators.

```
"AI weekly newsletter roundup this week" freshness:week
"best AI news this week agent framework developer tools" freshness:week
"site:huggingface.co papers trending this week" freshness:week
"AI engineering podcast summary this week Latent Space" freshness:week
```

**Curated Source Table** (search these via exa):

| Source | What to Search | Key Signal |
|--------|---------------|------------|
| **TLDR AI** | Weekly AI roundup | Broad coverage, mainstream+niche |
| **Ben's Bites** | Daily/weekly AI product news | Product launches, tools |
| **The Rundown AI** | AI news + explanations | Trending topics with context |
| **Import AI** | AI research and safety | Deep research, policy shifts |
| **Latent Space** | AI engineering podcast | Engineering-focused depth |
| **Last Week in AI** | Weekly summary newsletter | Comprehensive weekly coverage |
| **HuggingFace Papers** | Trending arxiv papers | Research frontier signals |
| **AI Brews** | Weekly founder/engineer roundup | Concise, actionable |
| **Simon Willison's Blog** | Practical AI/web dev | Production-grade insights |

**Cross-validation Rule**: If a topic appears in 2+ curated sources, flag it as a **high-confidence signal**.

---

## Layer 2 — Platform Hotness (OpenCLI Commands)

### Hacker News

```bash
$OPENCLI_CMD hackernews top --limit 50 -f json
$OPENCLI_CMD hackernews best --limit 30 -f json     # Higher quality signal
$OPENCLI_CMD hackernews new --limit 30 -f json       # Recency

# Fallback (if OpenCLI unavailable): HN Firebase API
# curl https://hacker-news.firebaseio.com/v0/topstories.json
# curl https://hacker-news.firebaseapp.com/v0/item/{id}.json
```

### X / Twitter

```bash
# Trending topics
$OPENCLI_CMD twitter trending -f json

# Search AI/Agent/Dev topics
$OPENCLI_CMD twitter search "AI agent framework" --limit 20 -f json
$OPENCLI_CMD twitter search "LLM MCP orchestration" --limit 20 -f json
$OPENCLI_CMD twitter search "AI developer tool" --limit 20 -f json

# KOL timelines
$OPENCLI_CMD twitter timeline --username {kol_handle} --limit 20 -f json
```

**Runtime Handle Discovery**: X/Twitter handles change and accounts get suspended. At the start of each run, use `mcp__exa__web_search_exa` to search "top AI influencers X Twitter 2026" and "most followed AI accounts on X" to discover current, verified handles.

### Reddit

```bash
$OPENCLI_CMD reddit hot --limit 30 -f json
$OPENCLI_CMD reddit subreddit LocalLLaMA --limit 20 -f json
$OPENCLI_CMD reddit subreddit MachineLearning --limit 20 -f json
$OPENCLI_CMD reddit subreddit AI_Agents --limit 20 -f json

# Fallback (if OpenCLI unavailable): exa search
# site:reddit.com/r/LocalLLaMA "this week" OR "just released" OR "new model"
# site:reddit.com/r/MachineLearning research OR paper OR "state of the art"
# site:reddit.com/r/AI_Agents agent OR automation OR workflow
```

### Product Hunt (exa search only — no OpenCLI adapter)

```
site:producthunt.com AI tool agent automation this week
```

---

## Layer 3 — Domain Deep Dive Queries

### Agent & Automation

```
"AI agent framework MCP multi-agent orchestration announcement this week"
"autonomous agent workflow harness engineering new release"
"agentic AI automation tool breakthrough this week"
"model context protocol MCP server tool new release"
```

### Developer Tools & Open Source

```
"AI developer tool open source new release this week"
"AI coding assistant new features update Claude GPT Copilot"
"developer productivity AI tool SDK launch"
"open source AI project gained stars this week"
```

### Industry Viewpoints & Analysis

```
"AI industry analysis opinion debate this week"
"artificial intelligence future outlook expert commentary"
"LLM reliability hallucination safety discussion this week"
"AI replacing developers argument opinion"
```

---

## Semantic Expansion

When searching, expand seed terms to catch related concepts:

- **"Agent"** → also search: MCP, multi-agent, orchestration, autonomous, agentic workflow, harness engineering
- **"LLM"** → also search: large language model, foundation model, GPT, Claude, Gemini, reasoning model
- **"Dev tool"** → also search: SDK, API, framework, library, CLI tool, developer experience