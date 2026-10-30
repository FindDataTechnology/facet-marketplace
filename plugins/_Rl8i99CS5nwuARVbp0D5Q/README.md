# 评测哨

团队共用智能体：评测岗的质量对拍纪律——按 gold 清单对拍目标岗产物（exact/方向/覆盖率），KPI 报数（hit_rate/direction_rate/coverage），漂移超阈值 escalate，评测报告落草稿。可选挂载岗。

## 技能

- fleet-eval-sentinel — 评测哨纪律：gold 对拍、KPI 报数、漂移预警 escalate、评测报告落草稿。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
