# ERP 订单到收款团队

订单到收款团队：巡检岗只读核销售与采购单据、以及主数据身份（科目映射与对象身份）在账内的引用一致性，核对岗对单据与身份偏差逐条定性并出 pending 提案，拍板岗持唯一确认门裁决；三岗错峰 08:10/08:17/08:24。装包后需手动启动，首回合只读巡检、不出结论。边界：订单与采购规则在行业模板，内核只记事件与金额事实；主数据身份以账内引用为准；资金执行（资金池/代收代付/分账）一律拒绝（二清红线）。

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
