# ERP 事实接入与数量面三岗岗位包

事实接入团队的岗位 preset 源：巡检岗（只读验收事件完整性与数量面水位）、核对岗（拟稿事件定性与补拟稿）、拍板岗（事实确认门与导入车道）。工具面引用 fd-erp-ledger-mcp 5 件，逐岗经 resources.mcpTools 收窄，包级 allowTools 只作封顶；数量面不可直写，资金执行一律拒绝（二清红线）。

## 技能

- erp-fact-intake-discipline — 事实接入三岗纪律：事件只追加、拟稿未过确认门不算事实、读取自证完整性、数量面不可直写、确认门只属拍板岗、资金红线。

## 智能体

- 巡检岗 — 只读验收事件完整性（截断/水位）与数量面 drift，产出巡检清单
- 核对岗 — 拟稿事件逐条定性，缺字段与冲突的以拟稿车道补齐
- 拍板岗 — 唯一事实确认门：裁决拟稿事件，并由导入车道接收外部原始事实

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
