# ERP 仓配分销三岗能力包

仓配分销模板的运行时岗位包：巡检岗（纯只读对账与单据巡检）、核对岗（四类差异定性，只到提案）、拍板岗（确认门裁决落账或拒绝留痕）。工具面引用 fd-erp-ledger-mcp 八件商业工具，按岗经 resources.mcpTools 收窄：巡检只读、核对到提案、拍板才持确认门——任何岗位都不存在绕过确认门直写账本的路径。纪律随包技能自带：账实一致以证据为准（在途引物流适配器、账实引 WMS 快照），外部系统未接通如实 NOT_WIRED 不伪造；资金归集/代收代付/分账一律拒绝（二清红线），开票不自研，BYOK 凭证零落库零日志。

## 技能

- erp-supply-recon-discipline — 仓配分销三岗纪律卡（巡检/核对/拍板）：账实一致以证据为准、核对只到提案、确认门只属拍板席位、资金红线与如实 NOT_WIRED。

## 智能体

- 巡检岗
- 核对岗
- 拍板岗

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-erp-ledger-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-erp-ledger-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
