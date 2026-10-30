# 识律营销团队

lex 营销内容团队模板（content-factory 变体）：采集位（客户场景与行业痛点）→合成位（量级话术，不用精确数字）→闸位（红线审读必设）→产出位（落官网/CMS 草稿，发布走动作闸提案不直发）。装包后经壹座「团队→从模板实例化」建队；纪律来自共用层四包（同 cell 装）：fleet-intel-gather/fleet-brief-report/fleet-data-qa/fleet-board-contribution。

## 智能体

- 采集位 — 客户场景与行业痛点选题
- 合成位 — 按量级话术口径成稿
- 闸位 — 红线审读（必设岗）
- 产出位 — 落官网/CMS 草稿，不直接发布

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
