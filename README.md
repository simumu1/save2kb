# save2kb 📥→📚

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/simumu1/save2kb?style=social)](https://github.com/simumu1/save2kb)
[![Hermes Agent](https://img.shields.io/badge/Hermes%20Agent-ready-8B5CFE)](https://hermes-agent.nousresearch.com)

**Web article → Knowledge Base.** Read → Summarize → Save. Auto-classify, multi-language, token-efficient.

任意网页文章（公众号/知乎/头条/博客/新闻）→ 转 Markdown → 自动分类 → 存到结构化知识库。纯文字存档，省 token。

---

## ✨ Features / 特性

| | |
|---|---|
| 🔄 **Auto-classify** | 自动判断文章主题标签（A股/量化/AI/宏观/SEO...） |
| 🌐 **Multi-language** | 用户说中文就用中文，说英文就用英文 |
| 💰 **Token-efficient** | 优先 web_extract，不行再启浏览器 |
| 📝 **Text-only archive** | 只存文字，图片保留在原链接 |
| 📂 **Flexible KB** | 支持现成知识库（Obsidian等），也可自动创建 |
| 📊 **Weekly digest** | 可 cron 定时生成主题周报 |

## 📦 Install / 安装

### Hermes Agent

```bash
# From marketplace
/plugin marketplace add simumu1/save2kb

# Or manual: clone to skills directory
git clone https://github.com/simumu1/save2kb.git ~/.hermes/skills/save2kb
```

### Claude Code / Codex / OpenCode

```bash
# Generic: copy to agent skills dir
git clone https://github.com/simumu1/save2kb.git ~/.agents/skills/save2kb

# Claude Code specific
cp -r save2kb ~/.claude/skills/save2kb
```

### npx skills

```bash
npx skills add https://github.com/simumu1/save2kb
```

## 🚀 Usage / 使用

### 场景 A：存知识库

> **用户：** `https://mp.weixin.qq.com/s/... 存知识库`
>
> **智能体：** 读文章 → 汇报要点 → 问"有现成知识库还是帮你建一个？" → 按分类存档

### 场景 B：只读不存

> **用户：** `https://zhuanlan.zhihu.com/p/... 你看这个`
>
> **智能体：** 读文章 → 汇报要点 → 结束（不存档不询问）

## 📁 Knowledge Base Structure / 知识库结构

```
~/知识库/
├── INDEX.md              ← 主索引（按时间倒排）
├── 原始文章/              ← 纯文字 MD，YYYY-MM-DD-标题.md
├── 主题归类/              ← 按主题聚合的笔记（A股/AI/SEO...）
└── 系列总结/              ← 每周自动生成的周报
```

每篇 MD 文件带 YAML frontmatter：

```yaml
---
title: "文章标题"
source: "来源网站"
url: "原文链接"
date: 2026-07-09
tags: [A股, 宏观]
summary: "文章摘要"
status: raw
---
```

## 🌐 Full Docs / 完整说明书

| Language | File |
|:---------|:-----|
| 🇨🇳 中文 | [README.zh-CN.md](references/README.zh-CN.md) |
| 🇬🇧 English | [README.en.md](references/README.en.md) |

## 📄 Skill Definition

[SKILL.md](SKILL.md) — the main skill file for AI agents to consume. Bilingual (CN/EN).

## ⚙️ Dependencies / 依赖

- **Camofox Browser** (optional) — for anti-scrape pages like WeChat. Auto-starts on `localhost:9377`
- **web_extract** — built-in agent tool, preferred for token efficiency

## 📜 License

MIT
