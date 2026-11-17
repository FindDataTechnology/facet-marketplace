# ERP 订单到收款三岗岗位包

订单到收款团队的岗位 preset 源：巡检岗（只读核单据与主数据引用）、核对岗（偏差定性与 pending 提案）、拍板岗（唯一确认门）。工具面引用 fd-erp-ledger-mcp 6 件，逐岗经 resources.mcpTools 收窄，包级 allowTools 只作封顶；订单规则在行业模板，资金执行一律拒绝（二清红线）。

## 技能

- erp-order-to-cash-discipline — 订单到收款三岗纪律：单据与账内事件对齐、主数据身份唯一、订单规则在行业模板、核对只到提案、确认门只属拍板岗、资金红线。

## 智能体

- 巡检岗 — 只读核单据与分录对齐、主数据身份唯一性，产出巡检清单
- 核对岗 — 单据与身份偏差逐条定性，结论以 pending 提案回写
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
