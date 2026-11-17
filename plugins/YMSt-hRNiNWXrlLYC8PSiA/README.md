# 投研晨会

官方团队模板：三层级联的投研晨会——首席分析师汇总宏观/行业研究员晨报并拍板上行，风控官为被动岗（受委托或拍板批复唤醒）。演示：三层组织（{{fleetContext}} 只注入直接下级，中间层向上浓缩）、交易日节奏、escalate 驱动的被动岗；编导段为 trigger+handoff 级联（两研究员晨检完成接棒首席）与首席 dispatch 委托风控官。铸造上架时 packId/agents 按铸造脚本补齐；本文件是清单真源，测试钉住其必须通过 fleet 段预校验。

## 技能

- invest-view-card — 研究员与首席的标准观点格式：观点、证据、反方、置信度四件套。
- morning-note-template — 首席合成晨会产出的标准模板：共识、分歧、待拍板、观察清单。
- invest-decision-log — 每轮产出判定即落账；到期项在复盘窗口结算并写反思。含基准纪律与「本轮无判定」写法。

## 智能体

- 首席分析师 — 交易日晨会汇总与裁决
- 宏观研究员 — 宏观面每日晨检
- 行业研究员 — 行业与标的每日晨检

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
