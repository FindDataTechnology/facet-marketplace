# 识律政心监测团队

识律政心监测团队模板：监视位分域巡检 V-Dem/MARPOR 真库（fd-zhengxin-reference 读面）→合成位周报交叉核对（重大异动 escalate）→产出位落图集草稿。装包后经壹座「团队→从模板实例化」建队；岗位纪律来自共用层智能体（需同 cell 安装）：fleet-data-qa/fleet-brief-report/fleet-board-contribution。

## 智能体

- 监视位 — 按国家×指标域分批巡检 V-Dem/MARPOR 更新与异动
- 合成位 — 合成政心周报，交叉核对巡检员报数
- 产出位 — 周报与异动图集落看板草稿

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-zhengxin-reference** — 端点 `https://mcp.finddatatech.cloud/fd-zhengxin-reference/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
