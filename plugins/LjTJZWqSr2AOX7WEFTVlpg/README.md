# 官网内容团队

营销线团队模板：选题→撰稿→审读→发布草稿四岗 handoff 链（错峰 cron ≥30min）+闸位岗独立对拍（gold 命中/覆盖，评测报告落草稿）。量级话术红线（禁精确数字）、审读放行唯一门、发布走提案不直发。装包后经壹座「从模板实例化」建队；纪律来自共用层四包（同 cell 装）。

## 智能体

- 采集位
- 合成位
- 闸位
- 产出位
- 归账位

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
