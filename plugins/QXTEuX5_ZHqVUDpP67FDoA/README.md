# ERP 发票与对账三岗岗位包

发票对账团队的岗位 preset 源：巡检岗（只读跑对账取四类差异）、核对岗（差异定性与 pending 提案）、拍板岗（唯一确认门）。工具面引用 fd-erp-ledger-mcp 7 件，逐岗经 resources.mcpTools 收窄，包级 allowTools 只作封顶；对账零外部写入，资金执行一律拒绝（二清红线）。

## 技能

- erp-invoice-recon-discipline — 发票对账三岗纪律：对账只读零外部写入、四类差异必带双侧引用、核对只到提案、确认门只属拍板岗、票据本体不在平台、资金红线。

## 智能体

- 巡检岗 — 只读跑两源对账，取四类差异与双侧引用，产出对账报告
- 核对岗 — 四类差异逐条定性并回写 pending 提案，证据不足就挂起
- 拍板岗 — 唯一确认门：逐笔裁决提案落账或拒绝留痕

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
