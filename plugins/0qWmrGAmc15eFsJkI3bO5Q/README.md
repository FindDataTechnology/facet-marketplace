# 柏讯 · 证券行业观察

证券行业观察主题源包：市场结构五族（两融/北向/成交估值/龙虎榜/协会经营，概念码 market.* 与 securities_industry.*）+ 行情族（price.*，id 233-238）与白名单个股财务指标对比（financials.*）。技能钉定指标目录主题切片，数据面=fd-open-data-mcp 在册工具（目录定位→序列读数→覆盖度自查）+ wire-ops 数据集读面。官方/聚合分层标注、期数必带、概念未在册如实降级、未取得不编造；输出不是投资建议。白名单=沪深300+中证500+全指证券公司成分合计 805 家。

## 技能

- securities-market-structure-read — 证券市场结构读数纪律：两融/北向/成交估值/龙虎榜/协会经营五族概念（market.*、securities_industry.*），官方与聚合分层标注、期数必带、概念未在册如实降级、未取得不编造。
- securities-quote-whitelist-read — 证券行情与白名单个股读数：price.* 行情族（id 233-238）+ financials.* 指标族同业对比，白名单 805 家（沪深300+中证500+证券公司成分）内取数，逐值带期数与来源分层。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`（需要组：wire-customers）
- **wire-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-ops-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
