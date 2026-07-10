---
name: save2kb
description: >
  Save web articles to a structured local knowledge base. Reads any public article
  (blogs, news, forums), converts to Markdown, auto-tags by topic,
  archives to disk. After reading, summarizes key points and offers to save with
  proper categorization. Use when user sends a URL/link, mentions saving articles,
  wants to build a knowledge base, or asks about organizing web content.
license: MIT
compatibility: Designed for Hermes Agent
metadata:
  author: simumu1
  repo: https://github.com/simumu1/save2kb
  languages: zh, en
---

# save2kb — Web Article → Knowledge Base

> **Bilingual:** When user speaks Chinese, reply in Chinese; English ↔ English.
> **Trigger:** User sends any article URL.

Save web articles to a local knowledge base as structured Markdown files.

## Quick workflow

```
User sends URL
    ↓
① Read article — web_extract → Camofox browser → search reposts → paid API
    ↓
② Report summary (key points, source, date)
    ↓
③ Offer to save? (auto-save if user said "知识库"/"save to KB")
    ├─ Yes → determine KB path → format as MD → archive
    └─ No  → done
```

## Reading priority (token-saving)

| Priority | Method | When |
|:---------|:-------|:-----|
| 1st | `web_extract(urls=[...], char_limit=10000)` | Open sites |
| 2nd | Camofox → `browser_navigate` | Anti-scrape sites (browser fallback)
| 3rd | `web_search` for reposted versions | When browser also fails |
| 4th | dajiala.com paid API (¥0.03/article) | Last resort |

## KB path logic

- First time: ask user for path or offer `~/知识库/` default
- Repeat: reuse previous path (persist in memory)
- Respect user's existing directory structure

## Save format

Each article → `~/知识库/原始文章/YYYY-MM-DD-short-title.md` with YAML frontmatter:

```yaml
---
title: "Original Title"
source: "Site/Author"
url: "https://..."
date: 2026-07-09
tags: [topic1, topic2]
summary: "≤200 chars"
status: raw
---
```

Then update `INDEX.md`, add to topic folder, set `status: classified` after weekly cron digest.

## Tags

Auto-detect: `A股`, `量化`, `AI`, `宏观`, `行业`, `科技`, `财经`, `SEO`, `其他`. Don't ask.

## Cron suggestion

Weekly cron: scan `status: raw` articles → group by tag → generate weekly digest → mark `classified`.

---

**Full docs:**
- [中文说明书](references/README.zh-CN.md)
- [English Docs](references/README.en.md)
