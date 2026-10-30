# 柏讯 · 数据治理

柏讯数据治理套件：读表单收上来的数据、改错一条记录、撤销改错、查改错历史、导出 CSV。写修正必须带来源标记（source）且不得代填；撤销只置回滚标记，原始行永不改写；导出带 truncated 字段，被行数闸截断时如实告知。失败原样转述，禁止以空列表冒充无数据。

## 技能

- wire-data-steward — 柏讯数据治理。当用户要读表单收上来的数据、改错一条记录、撤销一次改错、查改错历史或导出 CSV 时使用。写修正必须带来源标记（source），不得代填；撤销只置回滚标记、原始行永不改写；导出结果带 truncated 字段，true 表示被上游行数闸截断而非全量——必须如实转述。任何工具失败原样转述，禁止以空列表冒充无数据。

## 智能体

- 柏讯数据治理助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
