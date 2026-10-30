# 跨境电商政策盯梢团队

跨境电商政策面值守团队：监视位（海关/商务部/税务/口岸四路 fd-info 公告流）→合成位（政策日报+三判据 escalate）→产出位（看板草稿，发布走提案）。纪律来自共用层智能体（同 cell 装）；由蓝图构造间按自然语言需求建成，可溯源。

## 智能体

- 监视位
- 合成位
- 产出位

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
