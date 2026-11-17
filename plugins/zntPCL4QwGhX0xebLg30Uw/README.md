# ERP 计费规则与费用三岗岗位包

计费团队的岗位 preset 源：巡检岗（只读盘点规则与费用）、核对岗（重算试算与规则草稿，只出提案）、拍板岗（规则审批门与费用红冲）。工具面引用 fd-erp-ledger-mcp 8 件，逐岗经 resources.mcpTools 收窄，包级 allowTools 只作封顶；资金执行（资金池/代收代付/分账）一律拒绝（二清红线）。

## 技能

- erp-billing-discipline — 计费三岗纪律：规则版本化且人工审批上线、费率必须有出处、重算幂等、红冲保留原行、确认门只属拍板岗、资金红线。

## 智能体

- 巡检岗 — 只读盘点规则版本状态与费用明细四层溯源，标出无出处费率与快照不符
- 核对岗 — 重算费用、试算新旧规则差异、定性差异出提案，规则修订落草稿
- 拍板岗 — 唯一审批门：规则审批上线，错计费用红冲保留原行

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
