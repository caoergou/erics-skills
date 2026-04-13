---
name: ai-digest
description: "Weekly AI x Dev x Agent intelligence briefing. Curates trending Agent frameworks, developer tools, and industry viewpoints from HN, GitHub, Reddit, Twitter/X, and curated newsletters — delivered as a Chinese-language digest with source links and analyst commentary. Requires OpenCLI."
---

# AI Digest

Weekly intelligence briefing covering **AI, development, and Agent** ecosystems. Produces a Chinese-language digest that answers:

1. **What happened this week?** — Trending topics, frameworks, tools, and breakthroughs across all major sources.
2. **What's worth paying attention to?** — Curated picks with analyst-style commentary (pros, cons, who benefits).
3. **What are people arguing about?** — Industry viewpoints, debates, and contrarian takes.

The output is designed to be **directly shareable** — read in terminal, save as Markdown, or paste into team channels.

> **Scope**: This skill is for weekly intelligence gathering and analysis. It discovers **unknown unknowns** through curated discovery, not just keyword search.

---

## Prerequisites

### OpenCLI (Required)

This skill uses **[OpenCLI](https://github.com/jackwener/opencli)** (14.8K+ stars) as its primary data acquisition engine. OpenCLI provides CLI access to 79+ websites including Twitter/X, Hacker News, Reddit, and more.

**Install OpenCLI:**

```bash
# Install globally
npm install -g @jackwener/opencli

# Install Browser Bridge extension (required for Twitter/X)
# 1. Download opencli-extension.zip from https://github.com/jackwener/opencli/releases
# 2. Unzip and load in chrome://extensions (enable Developer mode first)
# 3. Ensure Chrome is running and logged into Twitter/X

# Verify setup
opencli doctor
```

**For OpenCLI Skill integration (recommended):**

```bash
npx skills add joeseesun/qiaomu-opencli-skills --skill qiaomu-opencli-usage
```

**Headless / Server Environments:**

```bash
# Install Xvfb (Debian/Ubuntu)
sudo apt-get install -y xvfb

# Option A: xvfb-run prefix
xvfb-run opencli hackernews top --limit 10 -f json

# Option B: Persistent virtual display
Xvfb :99 -screen 0 1280x1024x24 &
export DISPLAY=:99
opencli twitter trending -f json   # No prefix needed
```

> **Note**: Public API commands (`hackernews`, `devto`, `lobsters`, `stackoverflow`) work without browser setup. Headless mode with Xvfb covers `reddit` and most browser-backed commands. Twitter/X requires a logged-in Chrome session.

---

## Workflow

### Step 0: Pre-flight Check & Memory

**0a. OpenCLI Health Check** (MUST pass before proceeding):

```bash
# Auto-detect headless mode
if [ -z "$DISPLAY" ]; then
  OPENCLI_CMD="xvfb-run opencli"
  which xvfb-run || { echo "WARNING: No display and xvfb-run not found."; OPENCLI_CMD="opencli"; }
else
  OPENCLI_CMD="opencli"
fi

# Verify OpenCLI
which opencli || { echo "ERROR: OpenCLI not installed."; exit 1; }
$OPENCLI_CMD doctor

# Test public API (should always work)
$OPENCLI_CMD hackernews top --limit 1 -f json

# Test browser-backed commands (requires Chrome + extension + login)
$OPENCLI_CMD twitter trending -f json    # Requires Chrome + X login
$OPENCLI_CMD reddit hot --limit 5 -f json  # Works with Xvfb
```

**If browser-backed commands fail** (exit code 69 or 77):
- Public API commands still work without browser
- Reddit works with Xvfb
- Twitter/X requires a logged-in Chrome session — proceed without X data if unavailable

**0b. Read Previous Digests** (Memory):

1. **Local scan**: Scan current working directory for `ai-digest-*.md` files
2. **Feishu scan**: Search Feishu personal library for historical digests:
   ```bash
   lark-cli docs +search \
     --query 'intitle:"AI × 开发 × Agent 周报"' \
     --filter '{"only_title":true}' \
     --format json
   ```
3. For each Feishu digest found, fetch content:
   ```bash
   lark-cli docs +fetch --doc <doc_token> --format json
   ```
4. Build a **Coverage Memory** — merge topics from local files AND Feishu docs:
   - If same topic appears in both local and Feishu → merge into single entry (Feishu is source of truth)
   - For current week's digest already in Feishu → prompt user: overwrite / append / skip (see `references/lark-integration.md`)
5. During Steps 1-3, flag overlapping topics:
   - **Identical topic** → SKIP unless significant new development this week
   - **Similar topic** → Mark as **🔄 Update**, write as supplement referencing prior digest date
   - **New topic** → Proceed normally

> **Feishu prerequisite**: Ensure `lark-cli auth login` is done. If Feishu operations fail, fall back to local-only mode. See `references/lark-integration.md` for full details.

**Update format** (see `references/output-template.md` for full template):
```
🔄 Update from Week 2026-04-07:
{Title} 有了新进展——{1-2 句说明新变化}。
上期点评认为 {回顾上次判断}，本期 {确认/推翻/延伸}：
```

**Memory Rules**:
- Read ALL previous digest files, not just the most recent
- Match by project name, key phrase, or URL domain — not just exact title match
- If no previous digests exist, proceed normally

### Step 1: Layer 1 — Curated Discovery

**Purpose**: Discover topics you didn't know to search for, by leveraging human curators.

Fetch from curated newsletters and aggregators using `mcp__exa__web_search_exa`. See **`references/search-queries.md`** for all query templates and the curated source table.

Key behavior:
- Extract all mentioned topics, projects, and debates as **seed signals**
- If a topic appears in 2+ curated sources → flag as **high-confidence signal**
- Use `freshness:week` parameter to focus on recent content

### Step 2: Layer 2 — Platform Hotness (via OpenCLI)

**Purpose**: Validate seed signals with community-driven popularity data, and catch topics that curators missed.

**Primary tool**: OpenCLI. Use it for ALL platform data where a built-in adapter exists.

Fetch from community platforms **in parallel**. See **`references/search-queries.md`** for all OpenCLI commands and fallback methods, and **`references/source-tiers.md`** for platform → command mapping.

Key platforms:
- **Hacker News** — `$OPENCLI_CMD hackernews top/best/new`
- **X / Twitter** — `$OPENCLI_CMD twitter trending/search/timeline`
- **Reddit** — `$OPENCLI_CMD reddit hot/subreddit`
- **GitHub Trending** — `$OPENCLI_CMD github trending` (if plugin installed)
- **Product Hunt** — exa search only (no OpenCLI adapter)

X/Twitter content processing rules (see **`references/source-tiers.md`**):
- Default quality: Tier 2
- Verified KOL announcements: Tier 1
- Unverified speculation: Tier 3
- Always find original source link rather than X post alone

Nitter RSS fallback (only when OpenCLI browser commands fail): See **`references/source-tiers.md`** for instance priorities.

### Step 3: Layer 3 — Domain Deep Dive

**Purpose**: Ensure key domains are never missed, even if they don't trend.

Use semantically expanded keyword searches with `mcp__exa__web_search_exa`. See **`references/search-queries.md`** for all domain-specific query templates and semantic expansion rules.

Three domains to deep-dive:
1. **Agent & Automation** — MCP, multi-agent, orchestration, agentic workflow
2. **Developer Tools & Open Source** — AI coding assistants, new releases, SDK launches
3. **Industry Viewpoints & Analysis** — debates, safety discussions, expert commentary

### Step 4: Synthesize & Rank

**Deduplication**:
- Merge the same topic across sources (e.g., GitHub trending + HN front page + Reddit = one entry)
- Keep the highest-quality source link as primary, reference others as cross-validation

**Scoring**: See **`references/scoring-algorithm.md`** for the full formula.

```
Score = (frequency × 0.25) + (source_quality × 0.30) + (recency × 0.20) + (cross_validation × 0.25)
```

**Topic Selection**:
| Section | Count | Criteria |
|---------|-------|----------|
| 🔥 Top 5 | 5 | Highest scores across all categories |
| 🤖 Agent & Automation | 5-8 | Top from Agent domain |
| 🛠 Dev Tools & Open Source | 5-8 | Top from Dev Tool domain |
| 💡 Important Viewpoints | 3-5 | Top opinion/analysis pieces |
| ⚡ Quick News | 3-8 | One-liner updates |
| 📖 Extended Reading | 2-3 | Long-read recommendations |

### Step 5: Output & Save

**Commentary Style**: Every notable item must include **5-section analyst-style commentary**. See **`references/commentary-style.md`** for the full template and rules.

The 5 mandatory sections:
1. **背景与意义** — Why this matters now, what shift it signals
2. **谁该关注** — Who should care and in what scenario
3. **对比与定位** — How it compares to alternatives, what gap it fills
4. **硬伤与风险** — Specific limitations with data/version numbers
5. **趋势判断** — Likely trajectory and recommended action

Every item must also include 👍 (值得点) and 👎 (需注意) with verifiable specifics.

**Output Format**: See **`references/output-template.md`** for the complete Markdown template.

**File Saving** (two versions required):

| Version | Format | Location | Purpose |
|---------|--------|----------|---------|
| Local | Standard Markdown | Current working directory as `ai-digest-{YYYY-MM-DD}.md` | Local copy, Coverage Memory source |
| Feishu | Lark-flavored Markdown | Personal knowledge library (`--wiki-space my_library`) | Published version, shareable |

**Local save:**
1. Save standard Markdown to current working directory
2. Filename: `ai-digest-{YYYY-MM-DD}.md` (Monday's date of current week)
3. Confirm the file path to the user

**Feishu save:**

1. Convert content to Lark-flavored Markdown (see `references/lark-integration.md` for enhancement rules)
2. Title: `AI × 开发 × Agent 周报 | {YYYY-MM-DD}` (Monday's date)
3. If no existing digest for this week → **Create new**:
   ```bash
   lark-cli docs +create \
     --title "AI × 开发 × Agent 周报 | {monday_date}" \
     --wiki-space my_library \
     --markdown '{lark_flavored_content}'
   ```
4. If digest already exists for this week → **Follow user decision** (overwrite / append / skip), made in Step 0b
5. Confirm the Feishu doc URL to the user

> See `references/lark-integration.md` for full Feishu integration details, error handling, and Lark-flavored Markdown enhancement rules.

**Follow-up Options** (offer after saving):
1. **深挖** — Choose a specific news item for deeper analysis
2. **对比** — Compare 2-3 projects/frameworks
3. **搜索** — Deep-search a specific topic
4. **更新飞书** — Re-push or update the digest to Feishu

---

## Rules

### Search Rules
- **OpenCLI is the primary data acquisition tool** — use it first for all supported platforms
- Use `mcp__exa__web_search_exa` for: curated newsletter discovery, Product Hunt, GitHub Trending, anything OpenCLI doesn't cover
- Use `fetch_fetch`/`webfetch` only for Nitter RSS fallback
- Use HN Firebase API as fallback when OpenCLI is unavailable
- Search from sources **in parallel** for speed
- Every item **must** include a source link — no exceptions
- **Never fabricate content** — note gaps explicitly if a source is unreachable
- Time range: **past 7 days** from execution date
- Deduplicate aggressively — same topic across sources = one entry
- Scoring formula: frequency × 0.25 + quality × 0.30 + recency × 0.20 + cross_validation × 0.25

### Memory Rules
- ALWAYS read all existing `ai-digest-*.md` files AND search Feishu personal library before starting
- NEVER repeat a topic from a previous digest unless there is significant new development
- For recurring topics, use 🔄 Update format referencing the prior digest date
- Match topics by project name, key phrase, or URL domain — not just exact title match
- If same topic exists in both local and Feishu sources, merge into single Coverage Memory entry
- Feishu documents take priority as source of truth for "published" content
- If Feishu operations fail, fall back to local-only mode

### Commentary Rules
- **Have a stance** — NEVER write "既有机遇也有挑战"
- Each commentary must include ALL 5 sections (背景与意义, 谁该关注, 对比与定位, 硬伤与风险, 趋势判断)
- ALWAYS include both 👍 and 👎 with specific, verifiable points
- **Comparison is mandatory** — position every item against its alternatives
- Include concrete details: version numbers, star counts, test coverage %, competitor names
- If overhyped, say so directly. If undervalued, argue for why.
- See **`references/commentary-style.md`** for full rules and template

### Output Rules
- Titles stay in **English** (original language) for traceability
- Summaries and commentary in **Chinese**
- Date range uses Monday-Sunday of the current week
- 🔄 Update items reference the prior digest date: "🔄 Update from Week {date}"

---

## Reference Files

Detailed specifications are split into reference files under `references/`:

| File | Contents |
|------|----------|
| [`references/search-queries.md`](references/search-queries.md) | All search query templates for Layers 1-3, OpenCLI commands, semantic expansion rules |
| [`references/source-tiers.md`](references/source-tiers.md) | Source quality tiers (1-3), KOL tracking lists, Nitter fallback config, X content processing rules |
| [`references/scoring-algorithm.md`](references/scoring-algorithm.md) | Composite score formula, dimension weights, topic selection thresholds, deduplication rules |
| [`references/commentary-style.md`](references/commentary-style.md) | 5-section commentary template, update format, mandatory commentary rules |
| [`references/output-template.md`](references/output-template.md) | Complete Markdown output template, update format, file saving rules, follow-up options |
| [`references/lark-integration.md`](references/lark-integration.md) | Feishu integration: document naming, search/create/update commands, Lark-flavored Markdown enhancements, error handling |