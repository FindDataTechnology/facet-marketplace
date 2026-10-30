# 情报舆情集群（模板）

柏讯情报舆情集群模板：监视位→闸位双路并行→合成位交叉核对→产出位落看板草稿（trigger×3+handoff×3 编导）。装包后经壹座「集群→从模板实例化」建队；岗位纪律来自共用层四包（需同 cell 安装）：fleet-intel-gather/fleet-data-qa/fleet-brief-report/fleet-board-contribution。

## 智能体

- 监视位
- 闸位
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
