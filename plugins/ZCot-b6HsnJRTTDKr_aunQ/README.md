# 法规追踪团队

识律法规更新团队模板：监视位每日扫新颁/修订/废止（fd-legal-search-mcp 权威法规面，32,296 口径单一事实源）→合成位更新简报+生效影响标注（法条引用文号+条款定位可回查）→产出位落简报草稿；无更新日报空。装包后经壹座「团队→从模板实例化」建队；纪律来自共用层智能体（同 cell 装）：fleet-intel-gather/fleet-brief-report/fleet-board-contribution。

## 智能体

- 监视位 — 扫新颁/修订/废止，按 32,296 口径报数
- 合成位 — 更新简报 + 生效影响标注
- 产出位 — 简报落看板草稿

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-legal-search-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-legal-search-mcp/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
