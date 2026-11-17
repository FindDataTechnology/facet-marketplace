# 投研团队铸造舰

投研团队铸造舰的舰队模板：三岗错峰（巡检 08:30 / 设计 08:37 / 拍板 08:44）——巡检岗只读盘点本主名下每支投研团队的在岗情况与市场漂移；设计岗按缺口出模板蓝图草稿与现役编制草案并跑既有校验链干跑；拍板岗把已确认的变更做成提案送拍板队列。三岗都只到草稿与提案：巡检零写面、设计无批准铸造发布权、提案权只在拍板岗。

## 智能体

- 巡检岗 — 只读盘点在册投研团队编制、市场在架投研套件与版本、源↔市场漂移
- 设计岗 — 按缺口出模板蓝图草稿与现役编制草案，跑既有校验链干跑
- 拍板岗 — 唯一提案口：只读市场，把已确认变更做成提案送拍板队列

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **blueprint-author** — 端点 `https://mcp.finddatatech.cloud/blueprint-author/mcp`
- **facet-market-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-market-mcp/mcp`
- **actions** — 端点 `https://mcp.finddatatech.cloud/actions/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
