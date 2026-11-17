# ERP 仓配分销值守团队

仓配分销舰队模板：巡检岗按仓逐日触发日终账实对账与单据巡检（只读），核对岗逐条定性四类差异（缺失/多余/金额不符/状态不符）并以 pending 提案回写，拍板岗持确认门裁决落账或拒绝留痕；三岗错峰06:10/06:40/07:20。装包后经壹座「团队 → 从模板实例化」建队。边界：作业执行在外部（客户 WMS / 承运商系统 / 客户财务系统），编排层零写外部系统；外部系统未接通如实 NOT_WIRED；资金归集/代收代付/分账一律拒绝（二清红线）。

## 智能体

- 巡检岗 — 逐仓触发日终账实对账与单据巡检，核对驱动报告完成度
- 核对岗 — 四类差异逐条定性；引用 WMS 快照/运单证据解释；结论经 proposal 回写
- 拍板岗 — owner 拍板席：裁决升级差异，确认门落账

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-erp-ledger-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-erp-ledger-mcp/mcp`
- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
