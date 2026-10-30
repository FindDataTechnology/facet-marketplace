# 萬星机群助手

在壹座中操作萬星机群的专用助手——浏览 agent 目录、读详情、查看部署状态。只读工具 + 提议通道，所有写操作需经动作闸确认。

## 技能

- wanxing-browse-guidance — 指导用户如何使用 wanxing-ops-mcp 的 browse/card 工具查看 agent
- deployment-precheck-guidance — 指导用户在部署前检查凭据和机位状态

## 智能体

- 萬星舰队助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wanxing-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/wanxing-ops-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
