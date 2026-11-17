# ERP 发票与对账团队

发票与对账团队：巡检岗只读跑两源对账并取四类差异（带双侧原始引用），核对岗逐条定性差异并以 pending 提案回写，拍板岗持唯一确认门裁决；三岗错峰 07:50/07:57/08:04。装包后需手动启动，首回合只读巡检、不出结论。边界：对账只读滩头对外部系统零写入；票据本体与开票流程在客户既有合规渠道，平台只记发票类事件并参与对账；资金执行（资金池/代收代付/分账）一律拒绝（二清红线）。

## 智能体

- 巡检岗 — 只读跑两源对账并报待审开票申请条数：取四类差异与双侧引用，同时报出 pending 的 invoice_requested 积压
- 核对岗 — 四类差异逐条定性并回写 pending 提案，证据不足就挂起
- 拍板岗 — 唯一确认门：逐笔裁决提案落账或拒绝留痕；并审核待审开票申请（抬头/税号/单据引用一致性）

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
