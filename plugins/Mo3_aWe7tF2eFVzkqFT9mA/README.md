# 能力包·产出·看板投稿

集群共用能力包：投稿岗的产物写面纪律——wire-build 全套引用（建集/表单/记录视图/看板/文档/发布/治理），草稿优先、source_ref 三元组、列名预检、失败四分类。装配于任何板块的集群投稿岗。

## 技能

- fleet-board-contribution — 看板投稿纪律：草稿优先、source_ref 三元组、列名预检、失败四分类、零半写处理。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
