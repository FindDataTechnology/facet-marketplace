# 谦面市场助手

用 AI 直接操作谦面市场与网关：浏览/检索功集与网关条目、发布新功集与新版本、上下架、切可见性、策展分类、发布前查重。装包即得 facet_market_* 与 facet_agent_* 十二个工具（凭据走壹座既有连接：registry + 谦面市场配对），配两份手册技能讲清『怎么写 manifest、怎么过发布门、怎么安全写操作』。

## 技能

- facet-market-publishing — 谦面功集发布实操手册：manifest 写法、校验门、工具声明门、配额与版本纪律，发布失败逐条排障。
- facet-gateway-ops — 谦面网关（registry）操作手册：AgentCard 目录浏览/登记/更新/启停/查重的安全用法与错误语义。

## 智能体

- 谦面市场助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **facet-market-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-market-mcp/mcp`
- **facet-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-ops-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
