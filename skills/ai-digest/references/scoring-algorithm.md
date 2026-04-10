# Scoring Algorithm Reference

> This file defines the scoring formula and topic selection criteria used by ai-digest to rank and filter discovered topics.

---

## Composite Score Formula

```
Score = (frequency_score × 0.25) + (source_quality × 0.30) + (recency × 0.20) + (cross_validation × 0.25)
```

### Dimension Definitions

#### Frequency Score (weight: 25%)

How many independent sources mention the same topic.

| Sources | Points |
|---------|--------|
| 1 source | 1pt |
| 2 sources | 3pt |
| 3+ sources | 5pt |

#### Source Quality (weight: 30%)

The highest-quality source that covers this topic. See `source-tiers.md` for full tier definitions.

| Tier | Points | Examples |
|------|--------|----------|
| Tier 1 | 5pt | Anthropic/OpenAI blog, HN >200 upvotes, Simon Willison, verified AI KOL on X |
| Tier 2 | 3pt | GitHub Trending, Reddit Hot, Product Hunt, Latent Space, Ben's Bites |
| Tier 3 | 1pt | General news aggregators, random blogs, Nitter RSS, unverified X posts |

#### Recency (weight: 20%)

How recently the topic appeared or gained prominence.

| Timeframe | Points |
|-----------|--------|
| Within 24h | 5pt |
| Within 3 days | 3pt |
| Within 7 days | 1pt |

#### Cross-Validation (weight: 25%)

Independent verification across different source types (curated newsletter counts as 1 type).

| Independently Verified Sources | Points |
|-------------------------------|--------|
| Each independent source | +2pt |

**Independence rule**: Two Reddit posts in the same subreddit count as 1 source. A Reddit post + a HN thread + a newsletter mention = 3 independent sources (+6pt).

---

## Topic Selection Thresholds

| Section | Count | Selection Criteria |
|---------|-------|-------------------|
| 🔥 Top 5 | 5 items | Highest composite scores across all categories |
| 🤖 Agent & Automation | 5-8 items | Top-scoring from this domain |
| 🛠 Developer Tools & Open Source | 5-8 items | Top-scoring from this domain |
| 💡 Important Viewpoints | 3-5 items | Top opinion/analysis pieces |
| ⚡ Quick News | 3-8 items | One-liner updates, lower threshold |
| 📖 Extended Reading | 2-3 items | Long-read recommendations |

---

## Deduplication Rules

1. Merge the same topic across sources — e.g., a GitHub repo trending + HN front page + Reddit discussion = **one entry**
2. Keep the **highest-quality source link** as primary reference
3. Reference other sources as cross-validation metadata
4. If a curated newsletter covers a topic, it counts as Tier 2 quality signal