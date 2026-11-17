# 投研团队评测舰

投研团队评测舰的舰队模板：两岗错峰（评测 11:07 / 审计 11:37）——评测岗按客户自己的决策账本 gold 对拍在册投研团队，出 pending / overdue / hit_rate / as_of_gaps 四类读数；审计岗复核评测结论本身并把判词单独落库。评测方结构上不持账本写面（禁自证）：gold 只取账本与回合账本，不取岗位报数；隔离未成立时读数必须带标注，且不得呈现为某客户的命中率。

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
