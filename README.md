# save2kb 📥→📚

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![GitHub stars](https://img.shields.io/github/stars/simumu1/save2kb?style=social)](https://github.com/simumu1/save2kb)
[![Hermes Agent](https://img.shields.io/badge/Hermes%20Agent-ready-8B5CFE)](https://hermes-agent.nousresearch.com)

**Web article → Knowledge Base.** Read → Summarize → Save. Auto-classify, multi-language, token-efficient.

**✅ 轻松读取各网站文章 — 各主流网站，你只管发链接。**
**✅ Effortlessly reads articles from any site — various mainstream sites, just send the link.**

任意网页文章（各主流网站）→ 转 Markdown → 自动分类 → 存到结构化知识库。纯文字存档，省 token。

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

> ⚠️ **省的是LLM token，不是下载带宽。** 完整HTML该下载还是要下载，但save2kb在「下载」和「给AI看」之间做了提炼——把几百万字符的HTML→CSS/JS/div嵌套全部扔掉，只传几十~几百字的纯正文给AI。**省的是API计费的那个token，不是网络流量。**

```
原始HTML (百万字符) → 下载 → 正则/浏览器提取 → 纯文字 (千字符) → 给AI看
                           ↓                    ↓
                      带宽没法省            LLM token省99.9%
```

| 方式 | 下载完整HTML？ | 提炼后再给AI？ | LLM token消耗 |
|:----|:-------------|:--------------|:-------------|
| 🔴 原始HTML直接喂AI | ✅ 已下载 | ❌ 不提炼 | 几千token ❌ |
| 🟢 **save2kb** | ✅ 已下载 | ✅ 提炼正文 | **几十token ✅** |
| 🟡 其他提取工具 | ✅ 已下载 | ✅ 提炼 | 几十~几百token ✅ |

基于真实页面实测，分两种场景：

### 场景 A：国内部分网站→ 浏览器 compact snapshot

通过 Camofox 浏览器真实加载页面后，用 compact snapshot 只提取纯文字，避开页面中的技术框架代码。

| 读取方式 | 数据量 | 约合 Token | 倍数 |
|:---------|:------|:-----------|:----:|
| 🔴 浏览器完整页面源码 | **4,055,779 字符** | 数百万+ | **1,429x** |
| 🟢 **save2kb（compact snapshot → 纯文字 MD）** | **2,838 字符** | **~2,000-4,000** | **1x** |

> 📍 实测于一篇国内网站文章《每日SEO 198》，通过 DevTools 获取页面结构数据对比。
> 一篇文章即可节省 **99.93% token**。

### 场景 B：国际网站 → web_extract

通过 web_extract 直接抓取页面纯文字内容，跳过 HTML 结构标签。

| 网站 | 原始 HTML | 纯文字 | 节省 |
|:----|:---------:|:------:|:----:|
| The Verge（知名科技媒体） | 632,418 | 250,901 | **60%** |
| Simplified（内容营销博客） | 280,269 | 138,012 | **51%** |
| Ars Technica（老牌科技媒体） | 1,991 | 1,213 | **39%** |

> 国际网站平均节省约 **50%**。

### 总结

| 场景 | 节省幅度 | 原因 |
|:----|:--------:|:-----|
| 🏠 **国内网站** | **~99.9%** | 浏览器加载完整页面后，compact snapshot 只提取有效文字 |
| 🌍 **国际网站**（博客/新闻/媒体） | **~40-60%** | web_extract 直接提取纯文字，跳过 HTML 结构标签 |
| 📄 **技术文档类网站**（文档/手册） | **~20-40%** | 页面本身较精简，但去除导航和目录仍有节省 |

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
> **例 2：** `https://example.com/article/...`
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

- **Camofox Browser** (optional) — for anti-scrape pages. Auto-starts on `localhost:9377`
- **web_extract** — built-in agent tool, preferred for token efficiency

## 🙏 Acknowledgments / 致谢

本技能站在众多优秀开源项目和开发者的肩膀上，做了整合与优化。核心思路是保持简洁、省 token、省时间。如有改进建议，欢迎留言或提 Issue。

> This skill is built upon the work of many outstanding open-source projects and developers. Our role is integration and optimization. The core philosophy: simplicity, token efficiency, time saving. Feedback and suggestions are welcome.

**致谢名单 / Special Thanks to：**

| 项目 / 开发者 | 贡献 |
|:--------------|:-----|
| [Nous Research](https://nousresearch.com) | Hermes Agent — 智能体框架 / the agent framework |
| [jo-inc / Camofox Browser](https://github.com/jo-inc/camofox-browser) | 反检测浏览器，实现受限页面读取 / anti-detection browser |
| [kepano / obsidian-skills](https://github.com/kepano/obsidian-skills) | 技能结构参考 / skill structure reference |
| [obra / superpowers](https://github.com/obra/superpowers) | AI 编程技能框架 / AI coding skill framework (233k ⭐) |
| [jnMetaCode / superpowers-zh](https://github.com/jnMetaCode/superpowers-zh) | 中文技能文档范例 / Chinese skill documentation example |
| [anysearch-ai / anysearch-skill](https://github.com/anysearch-ai/anysearch-skill) | 公开技能 README 规范参考 / public skill README template |
| [极致了数据 dajiala.com](https://www.dajiala.com) | 国内受限页面 API（备选方案）/ Restricted page API (fallback) |

以及所有开源社区的贡献者。感谢你们的无私分享 🙌

## 📜 License

MIT
