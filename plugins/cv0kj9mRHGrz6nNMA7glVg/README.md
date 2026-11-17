# 跨境电商对账连接器包

跨境电商对账模板·连接器 pack：Shopify 店铺读取（复用已采纳 a1-x-tech/mcp-shopify-admin 的读面：订单 + 库存流（payouts 暂无目录覆盖，见绑定 note））+ 国内平台只读（mcp-cn-commerce，支持面以实测为准：淘宝/天猫/京东；拼多多上游 schema 缺失当前不提供）。凭证 BYOK 环境变量注入（ERP_BYOK_SHOPIFY_TOKEN / ERP_BYOK_CN_COMMERCE_TOKEN）零落库零日志。只读滩头：对外部系统零写入；真实店读写沿 crm-board 2026-10-30 裁决暂缓，待商家自带凭证逐店上线。运行时形态 = fd-erp-vertical-templates Python 包（对账驱动 sidecar），本包为市场分发与 pack v2 兼容清单。

## 技能

- erp-crossborder-recon-discipline — 跨境电商对账纪律卡：三方流对齐、差异四类双侧引用、多币种原币对账+出处汇率、费用独立维度、平台侧零写入、如实守界。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-erp-ledger-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-erp-ledger-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
