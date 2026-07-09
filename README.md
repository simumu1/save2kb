# save2kb 📥→📚

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/simumu1/save2kb?style=social)](https://github.com/simumu1/save2kb)
[![Hermes Agent](https://img.shields.io/badge/Hermes%20Agent-ready-8B5CFE)](https://hermes-agent.nousresearch.com)

**Web article → Knowledge Base.** Read → Summarize → Save. Auto-classify, multi-language, token-efficient.

**✅ 轻松读取各网站文章 — 公众号/知乎/头条/博客/新闻，你只管发链接。**
**✅ Effortlessly reads articles from any site — WeChat/Zhihu/Toutiao/Blog/News, just send the link.**

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

## 💰 Token Savings / Token 节省对比

基于真实页面实测（以一篇公众号文章为例）：

| 读取方式 | 数据量 | 约合 Token | 倍数 |
|:---------|:------|:-----------|:----:|
| 🔴 直接读原始 HTML（整页源码） | **4,055,779 字符** | 数百万+ | **1,429x** |
| 🟡 读页面全部 innerText（含无关文字） | 约 8,000-15,000 字符 | 5,000-10,000 | 3-5x |
| 🟢 **save2kb 方式（compact snapshot → MD 纯文字）** | **2,838 字符正文** | **~2,000-4,000** | **1x** |

**实测数据来源：** 同一篇微信公众号文章《每日SEO 198》，通过浏览器 DevTools 获取：
- `document.documentElement.outerHTML.length` = **4,055,779 字符**（整页 HTML，含 JS/CSS/跟踪代码）
- `document.body.innerText.length` = **2,838 字符**（纯文章正文）

**结论：** save2kb 比直接读整页 HTML 节省约 **99.93% 的 token**。不是"省一点"，是差了一千多倍。

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

**两种模式：**

| 你说 | 流程 |
|:----|:-----|
| 链接 + "知识库" | 读→汇报→**自动存档**（不问要不要） |
| 光链接 | 读→汇报→**问"要存吗？"** → 你说好再存 |

> **例 1：** `https://mp.weixin.qq.com/s/... 知识库`
> 读完 → 直接存档（只问路径）
>
> **例 2：** `https://zhuanlan.zhihu.com/p/...`
> 读完 → 问"要存知识库吗？"
> 你回"好" → 存；你回"不用" → 结束

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
