# 数据问答

团队共用智能体：数据问答/分析岗的只读纪律——数据集读面引用（列表/查询/聚合/行读），数字可溯源、口径注明、空结果如实、图表契约 v2 选型。只读，不含写面。

## 技能

- fleet-data-qa — 数据问答纪律：先探后答、数字可溯源、口径注明、图表契约选型、只读。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
