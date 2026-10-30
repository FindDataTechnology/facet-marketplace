# CRM 客服套件

商家自带 Chatwoot 的客服接待套件：连接你的 Chatwoot 账号后，读未处理会话、草拟回复、人工确认后发送，全程你自己的租户与凭据（BYOK）。含客服接待技能与使用指南。

## 技能

- crm-desk-reception-merchant — 客服接待（商家自带 Chatwoot 租户）：发现账号、读未处理会话、草拟回复、确认后发送并复核；失败四分类如实上报。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **chatwoot** — 端点 `https://mcp.finddatatech.cloud/chatwoot/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
