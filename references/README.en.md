# web-to-kb Skill Documentation (English)

## Overview

**web-to-kb** (Web → Knowledge Base) is a universal skill that reads any web article, converts it to Markdown, saves it to a structured knowledge base, and auto-classifies it by topic.

**✅ Effortlessly reads WeChat public account articles (mp.weixin.qq.com)** — anti-scraping handled automatically, just send the link.

## Use Cases

- User sends a WeChat/Zhihu/Medium/blog/news article link and says "save to knowledge base"
- Periodically collect industry articles and archive them into a knowledge base
- Generate weekly knowledge base digests

## Agent Workflow

### Trigger

User sends a URL + mentions "knowledge base" keywords (e.g., "save to KB", "add to knowledge base").

### Execution Steps

```
User sends link + "knowledge base"
    ↓
① Read article: web_extract → Camofox (auto-start) → browser_navigate
    ↓
② Report analysis (key points, core ideas)
    ↓
③ Ask about KB: "Save to KB? Have one or want me to create one?"
    ├─ "Have one (Obsidian/specific path)" → confirm path → auto-classify & save
    ├─ "No" → "Create one? (~/knowledge-base/ with raw articles/topics/summaries)"
    │           ├─ "Yes" → create → save
    │           └─ "No" → done
    └─ Previously confirmed → save directly without asking
```

### Reading Strategy (Token-Efficient)

| Priority | Method | Notes |
|:--------:|:-------|:------|
| 1 | web_extract | Fastest, set char_limit=10000. ⚠️ Some env backends are search-only, may fail |
| 2 | browser_navigate | Via Camofox anti-detection browser. Check localhost:9377 first, auto-start if down |
| 3 | web_search + reprints | Fallback when both above fail — search for reprints |

### Knowledge Base Structure

```
~/knowledge-base/
├── INDEX.md              ← Master index (reverse chronological)
├── 原始文章/              ← Raw article MDs, YYYY-MM-DD-title.md
├── 主题归类/              ← Topic-categorized notes
└── 系列总结/              ← Periodic digests/summaries
```

### Classification Rules

- Auto-detect tags from content (A-shares/Quant/AI/Macro/Industry/Tech/Finance/SEO/Other)
- Only ask user if they explicitly request a specific category
- Use 1-2 most relevant tags for multi-topic articles
- Can create new tags when needed

### When NOT to Save

User sends only a link (no "knowledge base" mention) → just read + analyze, no save, no questions.

## Dependencies

- **Camofox**: Local mode at `~/camofox-tmp/`, starts via npm on localhost:9377
- **web_extract**: Built-in Hermes tool, needs non-DDG backend for direct reads

## Notes

- **Text-only archive**: Only the article's text/markdown content is saved — no images, videos, or formatting styles. The original URL is preserved in the frontmatter for viewing images
- Skip duplicate URLs (check INDEX.md)
- Truncate filename to first 30 chars of title
- Camofox takes ~8s to start; subsequent reads are fast if kept running
