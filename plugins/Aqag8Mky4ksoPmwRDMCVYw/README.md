# 数据研究 · 生态版

官方功能集（生态首发）：FindData 数据底座（fd-open-data-mcp 宏观/金融真数据）+ 官方 time/fetch 生态 server + 数据研究 persona。装上即可做可溯源的数据问答与网页取证。

## 技能

- eco-skill-creator — [生态] 结构化创建新技能包（anthropics/skills，Apache-2.0）

## 智能体

- 数据研究员 — FindData 数据底座 + 生态工具的数据研究角色，可溯源、不编造。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`
- **eco-mcp-time** — 端点 `https://mcp.finddatatech.cloud/eco-mcp-time/mcp`
- **eco-mcp-fetch** — 端点 `https://mcp.finddatatech.cloud/eco-mcp-fetch/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
