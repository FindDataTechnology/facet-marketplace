---
name: "facet-market-publishing"
description: "谦面功集发布实操手册：manifest 写法、校验门、工具声明门、配额与版本纪律，发布失败逐条排障。"
---

# 谦面功集发布实操（facet-market-publishing）

## 你有哪些工具

- facet_market_pack_browse / read / versions：查目录、详情、版本史（author_me=true 只看自己的）
- facet_market_pack_publish：发布（省略 packId=新功集；带上 packId=发新版本，仅作者）
- facet_market_pack_visibility（public|private）/ facet_market_pack_curation（category 仅作者，featured 仅管理员）

## manifest 怎么写

必看：name（≤80 字）、description（≤2000）、tags（≤10×24 字）。可选：notes（版本注 ≤500）、category（slug）、guide.steps（恰好三步）。

- **skills**（≤10 个）：name 匹配 [A-Za-z0-9._-] 1-64 字、description ≤300 字、content 内联全文 ≤64KB。技能是『怎么用好』的方法论正文，不是文件引用。
- **mcpServers**（≤5 个）：只能写 { registryName, allowTools? }——url/headers/env 一律禁止（包永远引不进未注册服务）。allowTools 是工具叶名白名单（≤64 个，空名单会被拒）。
- **agents**（≤3 个）：persona-only——id/name/persona；baseUrl/model/apiKey 禁止。

## 发布门（会拦你的四道）

1. 清单校验：400 带逐条 errors，原样转述给用户改清单。
2. 工具声明门：声明了工具名的 mcpServers 会跟注册处对拍（对不上 400）；registry 不可达时 503 fail-closed——如实告知稍后重试。
3. 配额：仅通过校验的发布计数；429=稍后再试。
4. 权限：403 Creator group required=发布需 creators 组；403 Only the pack author=发新版本仅作者。

## 纪律

- 发布前必 facet_market_pack_browse 查重（同名/同用途先报告再问）。
- 新版本只改该改的：技能内容/清单字段；notes 写清本版变化。
- 失败如实转述：400 逐条、403 说权限、429 说配额、503 说上游——绝不静默重试写操作。
