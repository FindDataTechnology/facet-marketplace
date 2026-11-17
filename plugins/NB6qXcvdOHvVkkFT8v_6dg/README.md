# 投研团队评测两岗岗位包

投研团队评测舰的岗位 preset 源：评测岗（按客户账本 gold 对拍产物，报 pending/overdue/hit_rate/as_of_gaps 四类读数）与审计岗（对评测结论本身复核，判词单独落）。评测方结构上不持账本写面——`record_decision` / `settle_decision` 都不在工具面里，这是禁自证的可执行形式。

## 技能

- invest-grader-gold-discipline — 投研评测纪律：gold 只吃账本与回合账本不吃报数、逐条留两侧取值与口径、未取得不补零、按调用路径区分客户级与非客户级样本、隔离未成立必须标注、评测方不持任何账本写面。

## 智能体

- 评测岗 — 按客户账本 gold 对拍投研团队产物，出四类读数与偏离结论
- 审计岗 — 复核评测结论本身，判词单独落库

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`
- **fleet** — 端点 `https://mcp.finddatatech.cloud/fleet/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
