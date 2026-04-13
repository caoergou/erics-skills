# Lark (飞书) Integration Reference

> This file defines how ai-digest integrates with Feishu (Lark) for reading historical digests and writing new ones.

---

## Prerequisites

### lark-cli (Required)

This integration uses **lark-cli** to interact with Feishu documents. Ensure lark-cli is installed and authenticated before running ai-digest with Feishu features.

**Authentication check:**

```bash
lark-cli auth login
```

> See `lark-shared` skill for full authentication and permission details.

### Required Scopes

| Scope | Purpose |
|-------|---------|
| `search:docs:read` | Search for historical digests |
| `docx:document` | Read document content |
| `docx:document:create` | Create new documents |
| `docx:document:write` | Update existing documents |

---

## Document Naming Convention

All ai-digest documents in Feishu follow a strict naming convention for searchability and deduplication.

**Title format:**

```
AI × 开发 × Agent 周报 | YYYY-MM-DD
```

- Fixed prefix: `AI × 开发 × Agent 周报 | `
- Date: Monday of the current week (ISO 8601 format `YYYY-MM-DD`)
- Example: `AI × 开发 × Agent 周报 | 2026-04-13`

**Search query pattern:**

```bash
lark-cli docs +search --query 'intitle:"AI × 开发 × Agent 周报"' --filter '{"only_title":true}' --format json
```

**Date extraction rule:** The date portion after ` | ` is always the Monday of that week. Use this to:
- Determine if the current week's digest already exists
- Sort and compare historical digests by date

---

## Step 0b: Read Previous Digests (Feishu)

### Purpose

In addition to scanning local `ai-digest-*.md` files, ai-digest MUST also search Feishu personal library for historical digests to build a complete Coverage Memory.

### Procedure

**Step 1: Search for existing digests in Feishu**

```bash
# Search personal library for all ai-digest documents
lark-cli docs +search \
  --query 'intitle:"AI × 开发 × Agent 周报"' \
  --filter '{"only_title":true}' \
  --format json
```

If the user has specified a different knowledge space, use `--filter '{"space_ids":["<space_id>"]}'` instead.

**Step 2: Check for current week's digest**

From the search results, examine each document's title and extract the date. Compare with the current week's Monday date:

- **If a document with the current week's Monday date exists** → Prompt the user:
  ```
  ⚠️ 本周周报已存在于飞书：
  - 标题：AI × 开发 × Agent 周报 | 2026-04-13
  - 链接：https://xxx.feishu.cn/docx/xxx
  
  请选择操作：
  1. 覆盖 — 用新内容完全替换（使用 overwrite 模式）
  2. 追加 — 在现有文档末尾追加新内容（使用 append 模式）
  3. 跳过 — 保留现有文档，仅保存本地文件
  ```

- **If no current week's digest exists** → Proceed normally (create new)

**Step 3: Read historical digests for Coverage Memory**

For each historical digest document found (including the current week's if it exists):

```bash
# Fetch document content as Markdown
lark-cli docs +fetch --doc <doc_token_or_url> --format json
```

From the fetched content, extract:
- Previously covered topics (project names, key phrases, URLs)
- Prior analyst commentary and judgments
- Dates of previous coverage

**Step 4: Merge Coverage Memory**

Combine Coverage Memory from three sources:

| Source | How to Process |
|--------|---------------|
| Local `ai-digest-*.md` files | Existing behavior — scan filenames and content |
| Feishu historical digests | New — extract topics, URLs, commentary from fetched content |
| Deduplication across sources | If same topic appears in both local and Feishu, merge into single entry with all source references |

**Memory Rules (extended):**
- Read ALL previous digest files (local + Feishu), not just the most recent
- Match by project name, key phrase, or URL domain — not just exact title match
- If a topic appears in both local and Feishu sources, count them as the same topic (not duplicate)
- Prioritize Feishu content for Coverage Memory if both exist (Feishu is the source of truth for "published" content)

---

## Step 5: Save to Feishu (Write)

### Purpose

After generating the digest, ai-digest MUST save it to Feishu personal library in addition to the local file.

### Procedure

**Condition A: Current week's digest does NOT exist in Feishu → Create new document**

```bash
lark-cli docs +create \
  --title "AI × 开发 × Agent 周报 | {monday_date}" \
  --wiki-space my_library \
  --markdown '{full_digest_content_lark_flavored}'
```

**Return value handling:**
- Capture `doc_id` and `doc_url` from the response
- Report the Feishu document URL to the user

**Condition B: Current week's digest already exists → Update based on user choice**

User chose **覆盖 (Overwrite)**:
```bash
lark-cli docs +update \
  --doc <existing_doc_id> \
  --mode overwrite \
  --new-title "AI × 开发 × Agent 周报 | {monday_date}" \
  --markdown '{full_digest_content_lark_flavored}'
```

User chose **追加 (Append)**:
```bash
lark-cli docs +update \
  --doc <existing_doc_id> \
  --mode append \
  --markdown '{additional_content_lark_flavored}'
```

User chose **跳过 (Skip)**: Do not modify Feishu document. Only save local file.

---

## Lark-flavored Markdown Enhancements

When writing to Feishu, enhance the standard Markdown output with Lark-flavored features for better readability. These enhancements are ONLY applied to the Feishu version; the local Markdown file remains in standard Markdown.

### Enhancement Rules

| Standard Markdown | Lark-flavored Enhancement |
|-------------------|---------------------------|
| `> 📅 date info` header | Wrap in `<callout emoji="📅" background-color="light-blue">` block |
| `🔥 本周最热` section header | `<callout emoji="🔥" background-color="light-red">` for top items |
| Bullet point lists with 👍/👎 | Use `<grid cols="2">` for side-by-side 👍 vs 👎 comparison |
| Source links `[→ 原文](url)` | Keep as standard links (no change needed) |
| Section dividers `---` | Keep as standard dividers (no change needed) |

### Enhancement Examples

**Original (Standard Markdown):**
```markdown
> 📅 2026-04-13 - 2026-04-19 | 扫描源：Hacker News, X/Twitter, Reddit, GitHub, Product Hunt
> 📊 共扫描 150 条信息，去重后 80 条，精选 25 条

## 🔥 本周最热

### 1. NewFramework v2.0 Release
...
👍 值得点：轻量无重依赖
👎 需注意：文档不全
```

**Enhanced (Lark-flavored Markdown for Feishu):**
```markdown
<callout emoji="📅" background-color="light-blue">
2026-04-13 - 2026-04-19 | 扫描源：Hacker News, X/Twitter, Reddit, GitHub, Product Hunt
📊 共扫描 150 条信息，去重后 80 条，精选 25 条
</callout>

## 🔥 本周最热

### 1. NewFramework v2.0 Release
...
<grid cols="2">
<column>

👍 **值得点**
- 轻量无重依赖
- 5 分钟 Hello World

</column>
<column>

👎 **需注意**
- 文档不全（~60%）
- 测试覆盖率低

</column>
</grid>
```

### When to Apply Enhancements

- **Local file**: Standard Markdown (no Lark-flavored tags)
- **Feishu document**: Lark-flavored Markdown (with callout, grid, etc.)
- Both versions contain the SAME content; only formatting differs

---

## Error Handling

### Authentication Errors

If `lark-cli` returns authentication errors (e.g., `Permission denied`, `scope not granted`):

1. Remind user to run `lark-cli auth login`
2. Fall back to local-only mode (skip Feishu operations)
3. Continue with the digest generation and local file saving

### Feishu Search Failures

If `docs +search` fails:

1. Log the error but do not abort
2. Fall back to local-only Coverage Memory (scan local files only)
3. Add a note in the digest: `⚠️ 飞书搜索失败，Coverage Memory 可能不完整`

### Feishu Write Failures

If `docs +create` or `docs +update` fails:

1. Log the error with full error message
2. Attempt retry once
3. If retry fails, fall back to local-only mode
4. Save the Lark-flavored Markdown locally as `ai-digest-{YYYY-MM-DD}-feishu.md` for manual upload later
5. Report to user: `⚠️ 飞书写入失败，周报已保存为本地文件。可手动上传至飞书。`

### Feishu Fetch Failures (Reading)

If `docs +fetch` fails for a specific historical digest:

1. Skip that document but log the error
2. Continue processing other documents
3. The Coverage Memory may be incomplete — note this in the digest output

---

## Quick Reference: Command Cheat Sheet

| Operation | Command |
|-----------|---------|
| Search digests | `lark-cli docs +search --query 'intitle:"AI × 开发 × Agent 周报"' --filter '{"only_title":true}' --format json` |
| Fetch digest content | `lark-cli docs +fetch --doc <doc_token> --format json` |
| Create new digest | `lark-cli docs +create --title "AI × 开发 × Agent 周报 \| {date}" --wiki-space my_library --markdown '{content}'` |
| Overwrite existing | `lark-cli docs +update --doc <doc_id> --mode overwrite --new-title "AI × 开发 × Agent 周报 \| {date}" --markdown '{content}'` |
| Append to existing | `lark-cli docs +update --doc <doc_id> --mode append --markdown '{content}'` |

> **Note:** In bash, the `|` character in the title must be properly quoted or escaped.