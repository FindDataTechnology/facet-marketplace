# ERP 应收应付巡检团队

应收应付与总账巡检团队：巡检岗逐科目核借贷平衡、余额重算与多币种汇率出处（只读），核对岗逐条定性差异并以 pending 提案回写，拍板岗持唯一确认门裁决落账或拒绝留痕；三岗错峰 06:12/06:19/06:26。装包后需手动启动，首回合只读巡检、不出结论。边界：工具面仅 fd-erp-ledger-mcp 的 8 件且逐岗经 resources.mcpTools 收窄；汇率无出处即拒绝（FX_UNSOURCED），不猜折算；资金执行（资金池/代收代付/分账）一律拒绝（二清红线），资金类科目只出现在只读核对里。

## 智能体

- 巡检岗 — 逐科目核借贷平衡、余额重算与多币种汇率出处，产出巡检清单
- 核对岗 — 四类差异逐条定性，引用双侧原始分录；结论以 pending 提案回写
- 拍板岗 — 唯一确认门：逐笔裁决提案落账或拒绝留痕，并复核落账后余额

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
