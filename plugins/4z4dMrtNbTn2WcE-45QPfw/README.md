# 能力包·采集·情报采集

集群共用能力包：情报巡检岗的采集纪律——fd-info 检索面全套引用（list_since/get_item/search_info/locate_evidence/stream_health），按源清单巡检、原文先行、去重要点化、空结果带原因、未取得不编造。

## 技能

- fleet-intel-gather — 情报采集纪律：按源清单巡检、原文先行、去重要点化、空结果带原因分类。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
