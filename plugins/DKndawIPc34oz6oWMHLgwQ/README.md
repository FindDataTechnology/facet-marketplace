# ERP 信用额度三岗岗位包

信用额度团队的岗位 preset 源：巡检岗（只读核额度位与占用到期）、核对岗（孤儿占用定性与释放建议）、拍板岗（占额与释放）。工具面引用 fd-erp-ledger-mcp 6 件，逐岗经 resources.mcpTools 收窄，包级 allowTools 只作封顶；额度为备忘不写分录，资金执行一律拒绝（二清红线）。

## 技能

- erp-credit-discipline — 信用额度三岗纪律：额度由分录与占用派生、占用为备忘不写分录、到期与孤儿占用必查、确认门只属拍板岗、资金红线。

## 智能体

- 巡检岗 — 只读核额度位、占用到期与孤儿占用，产出巡检清单
- 核对岗 — 用分录复核应收余额与占用，孤儿占用逐条定性与释放建议
- 拍板岗 — 唯一写面：占额与释放，动作后复核可用额度

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
