---
name: save2kb
description: 把网页文章提取干净文字存到本地知识库，以后查从本地翻，0token。Save web articles to KB: text or text+images, 99% token savings.
created: 2026-07-09
tags: [knowledge-base, token-saving, web-extract]
---

# save2kb — 节省token的知识库工具

> 开源仓库：https://github.com/simumu1/save2kb

## 使用规则（三档）— Usage Rules

用户发链接时的行为 / When user sends a link:

| 用户说 / User says | 行为 / Behavior |
|:------|:-----|
| **「素材」/ "material"** | 📦 连文带图存到知识库 — Save text + images to KB |
| **「存」/ "save"** | 📄 只存文字到知识库 — Save text only to KB |
| **啥也不说 / nothing** | 👀 提取重点汇报，不存 — Extract & summarize, no save |



## 省token的原理

每次发链接让AI读，完整HTML已经被下载到本地/服务器，这个带宽成本省不了。
但save2kb省的是**LLM token**——在把内容传进AI上下文之前，先把HTML提炼成纯文字。

```
原始HTML (3,072,161字符) → 下载 → 正则提取 → 纯文字 (2,000字符) → 给AI看
                              ↓                    ↓
                         带宽没法省            LLM token省99.9%
```

### 三种方式的token消耗对比

| 方式 | 下载完整HTML？ | 提炼后再给AI？ | LLM token | 推荐？ |
|:----|:-------------|:--------------|:----------|:------|
| 🔴 原始HTML直接给AI | ✅ 已下载 | ❌ 不提炼 | 几千token | 不推荐 |
| 🟢 **save2kb = curl+正则** | ✅ 已下载 | ✅ 提炼正文 | **几十token** | ✅ **推荐** |
| 🟡 web_extract / browser | 后端做了一次 | ✅ 提炼 | 几十~几百token | ✅ 也省 |

**核心逻辑：**
- 下载完整HTML不可避（你不下载就不知道里面有什么）
- 但把几百万字符的原始HTML塞进AI上下文才叫浪费token
- save2kb在「下载」和「给AI看」之间做了提炼这步

### 实测验证

以微信公众号文章为例（2026-07-12实测）：
```
拿到的原始HTML:   3,072,161 字符 → ~6,144 tokens
提取后纯文字:     ~2,000 字符   → ~500 tokens
节省:             99.9%
```

那3,070,000字符的CSS/JS/div嵌套/反爬检测代码全部扔掉，AI只看到文章正文。

## 使用方式

```bash
git clone https://github.com/simumu1/save2kb.git ~/.hermes/skills/save2kb
```

## 存储目标

文章提取后的干净文字存到 save2kb skill 的 `references/` 目录：
```bash
skill_manage(action='write_file', name='save2kb',
  file_path='references/主题-日期.md',
  file_content='...')
```

## 带图保存（文章+配图）

可连文带图一起抓下来，图片存本地、Markdown里用相对路径引用。

**微信公众号：**
```bash
curl -sL -H "User-Agent: Mozilla/5.0" "https://mp.weixin.qq.com/s/xxx" \
  | python3 -c "
import sys, re, html, os, urllib.request, urllib.parse

raw = sys.stdin.read()

# 1) 提取正文
m = re.search(r'id=\"js_content\"[^>]*>(.*?)</div>\s*<script', raw, re.DOTALL)
body = m.group(1) if m else ''

# 2) 提取并下载图片
img_dir = 'references/images/' + urllib.parse.urlparse('https://mp.weixin.qq.com/s/xxx').path.split('/')[-1].replace('?','_')
os.makedirs(img_dir, exist_ok=True)

imgs = re.findall(r'<img[^>]+data-src=\"([^\"]+)\"', raw)
for i, url in enumerate(imgs):
    try:
        req = urllib.request.Request(url, headers={'Referer': 'https://mp.weixin.qq.com'})
        data = urllib.request.urlopen(req, timeout=10).read()
        ext = url.split('.')[-1].split('?')[0][:4] or 'jpg'
        fname = f'{img_dir}/img_{i+1}.{ext}'
        with open(fname, 'wb') as f:
            f.write(data)
        # 替换body中的图片URL为本地路径
        body = body.replace(url, fname)
    except:
        pass  # 下不动的保留原URL

# 3) 正文去HTML标签
text = re.sub(r'<[^>]+>', '', body)
text = html.unescape(text)
text = re.sub(r'\s+', ' ', text).strip()
print(text)
" > article.md
```

**通用网页（用 Python requests）：**
```bash
curl -sL -H "User-Agent: Mozilla/5.0" \"$URL\" \
  | python3 -c "
import sys, re, os, urllib.request, urllib.parse

raw = sys.stdin.read()

# 提取正文区域（可自定义选择器）
m = re.search(r'<article[^>]*>(.*?)</article>', raw, re.DOTALL)
if not m:
    m = re.search(r'<div[^>]*class=\"[^\"]*content[^\"]*\"[^>]*>(.*?)</div>', raw, re.DOTALL)

img_dir = 'references/images/' + urllib.parse.urlparse('$URL').path.split('/')[-1].replace('?','_')
os.makedirs(img_dir, exist_ok=True)

# 提取 <img> 标签
body = m.group(1) if m else raw
imgs = re.findall(r'<img[^>]+src=\"([^\"]+)\"', body)
for i, url in enumerate(imgs):
    if url.startswith('//'): url = 'https:' + url
    if not url.startswith('http'): continue
    try:
        data = urllib.request.urlopen(url, timeout=10).read()
        ext = url.split('.')[-1].split('?')[0][:4] or 'jpg'
        if len(ext) > 4 or ext not in ('jpg','jpeg','png','gif','webp','svg','ico'):
            ext = 'jpg'
        fname = f'{img_dir}/img_{i+1}.{ext}'
        with open(fname, 'wb') as f:
            f.write(data)
        body = body.replace(url, fname)
    except:
        pass
print(re.sub(r'<[^>]+>', '', body)[:5000])
"
```

抓下来的目录结构：
```
references/images/xxx/
  img_1.jpg
  img_2.png
  article.md   ← 引用的路径是 images/xxx/img_1.jpg
```

## 提取各类链接的方法

**微信文章（mp.weixin.qq.com）：**
公众号文章需要 curl + 正则提取，因服务器 IP 可能被限：
```bash
curl -sL -H "User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64)" \
  "https://mp.weixin.qq.com/s/xxx" \
  | python3 -c "
import sys, re, html
raw = sys.stdin.read()
m = re.search(r'id=\"js_content\"[^>]*>(.*?)</div>\\s*<script', raw, re.DOTALL)
if m:
    text = re.sub(r'<[^>]+>', '', m.group(1))
    text = html.unescape(text)
    text = re.sub(r'\\s+', ' ', text).strip()
    print(text)
" > article.txt
```

**GitHub README / docs：**
```bash
curl -sL "https://raw.githubusercontent.com/xxx/xxx/main/README.md" | head -200
```

**通用网页：**
用 `web_extract` 工具（当配置了 Firecrawl/Tavily 后端时优先用）。

## 效果

实测数据：
- InfoQ中文：原始624,648字符 → 纯文字9,479，省98.5%
- TechCrunch：原始209,767字符 → 纯文字2,736，省98.7%
- The Verge：原始84,457字符 → 纯文字1,919，省97.7%

## 已存资料

| 文件 | 内容 | 日期 |
|:-----|:-----|:----:|
| `references/amazon-google-ads-arbitrage-2026-07.md` | Amazon联盟+Google Ads搜索套利笔记 | 2026-07-12 |
| `references/openclaw-overseas-content-strategy.md` | OpenClaw海外英文10篇选题分级+运营规则 | 2026-07-12 |
| `references/claude-opus46-writing-逆向工程.md` | Opus 4.6写作能力拆解（写前三判断+五层自检） | 2026-07-12 |
| `references/xiaoxiao-8-prompts-humanize-2026-07.md` | 晓晓晓晓哇8提示词去AI味体系 | 2026-07-12 |
