# 跨境电商值守岗包

跨境电商行业线四大值守岗位：合规分流、关务单证要素、选品参谋、物流值守。三件套=职责边界/工作纪律/输出契约；mcpServers 仅引用在册三服务；allowTools ⊆ 试点同款清单。

## 技能

- crossborder-compliance-triage — 合规分流：新征/调口径/技贸壁垒/多源不一致三判据逐条分流、只分流不裁决、缺位如实标、escalate 走拍板通道。
- crossborder-customs-doc-elements — 关务单证要素：9610/1210/9710/9810 单列申报要素/单证/填报口径，要素只从原文/税则摘录，不确定标「原文未释」。
- crossborder-product-scout — 选品参谋：HS6×国别流向+汇率+技贸壁垒做需求画像；平台侧热销/店铺数据未授权即标「不可得（未授权）」，绝不臆测。
- crossborder-logistics-watch — 物流值守：portwatch/物流景气读水位、异常按口径升级、数值只引原文/原库、缺位如实标「未取得：<原因>」。

## 智能体

- 合规分流员
- 关务单证要素岗
- 选品参谋岗
- 物流值守岗

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
