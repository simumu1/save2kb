# save2kb 📥→📚

**Web article → Knowledge Base. Read → Summarize → Save. Auto-classify, multi-language.**

把任意网页文章（公众号/知乎/头条/博客/新闻）转成 Markdown，自动分类存到结构化知识库。

---

## What is this? / 这是什么？

An **AI Agent skill** for [Hermes Agent](https://hermes-agent.nousresearch.com). When the user sends a web article link + says "save to knowledge base", the agent:

- Reads the article (token-efficient)
- Reports a summary back to the user
- Converts it to Markdown (text only — no images)
- Saves to a structured knowledge base with auto-classification
- Supports periodic digests (weekly topic summaries)

---

## Quick Start / 快速使用

### For AI Agents / 给智能体

Load the skill and the agent will automatically handle any article link tagged with "knowledge base" / "知识库".

**Workflow:**
1. User sends a URL + says "save to KB" / "存知识库"
2. Agent reads the article
3. Agent reports the analysis
4. Agent asks: "Have a KB? Or create one?"
5. Agent saves + classifies

### For Humans / 给人看

This repo contains the skill definition file (`SKILL.md`) and documentation in multiple languages.

```
save2kb/
├── SKILL.md                      ← Skill definition (bilingual: CN/EN)
└── references/
    ├── README.zh-CN.md           ← 中文说明书
    └── README.en.md              ← English docs
```

---

## Knowledge Base Structure / 知识库结构

Default path: `~/知识库/` (configurable)

```
~/知识库/
├── INDEX.md              ← Master index
├── 原始文章/              ← Raw articles (YYYY-MM-DD-title.md)
├── 主题归类/              ← Topic-categorized notes
└── 系列总结/              ← Weekly digests
```

---

## Key Features / 核心特性

| Feature | Detail |
|---------|--------|
| 🔄 **Auto-classify** | Tags articles by content (A-shares/Quant/AI/Macro/SEO...) |
| 🌐 **Multi-language** | Works in any language — responds in user's language |
| 💰 **Token-efficient** | Prioritizes web_extract; falls back to browser only when needed |
| 📝 **Text-only** | Saves text content only — images available via original URL |
| 📂 **Flexible KB path** | Works with existing knowledge bases (Obsidian, custom paths) |
| 📊 **Weekly digests** | Can auto-generate topic-summary reports |

---

## Environment / 环境依赖

- **Hermes Agent** (or compatible AI agent framework)
- **Camofox Browser** (for anti-scrape pages like WeChat)
  - Runs locally on `localhost:9377`
  - Auto-started by the agent if not running

---

## License

MIT
