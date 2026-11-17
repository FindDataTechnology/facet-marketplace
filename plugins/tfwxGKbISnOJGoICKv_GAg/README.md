# 门店巡检三岗

门店巡检三岗团队包：巡店记录（只记录不改动）→ 整改跟踪（一件一单、状态三值）→ 周报复盘（完成率与复发口径固定）；三岗错峰 ≥30 分钟、fd-memory handoff 交接、每岗收尾 report_status；门店台账与整改回执两源需开户，随包移交。

## 技能

- store-inspect-record — 巡店岗逐店巡检记录技能：每日 08:10 触发后按门店清单逐店记录营业状态/陈列/缺货，事实清单体、只记录不改动；产出 inspection/{date}/{storeId} 逐店记录、inspection/{date} 交接与收尾报数。门店台账未开户时如实报 blocker，不编造门店。
- rectify-ticket-track — 整改岗一件一单跟踪技能：每日 08:40 认领巡店岗交接，按巡检记录逐条异常开整改单并跟踪三值状态（待整改/已整改/逾期）；产出 rectification/{date}/{issueId} 整改单、rectification/{date} 交接与收尾报数。回执源未开户时状态维持「待整改」并如实报 blocker，不推断已整改。
- review-weekly-report — 复盘岗周度汇总技能：每周一 09:20 触发后按周检索本周整改单，按固定口径算完成率/逾期数/复发数并列复发清单，落档 weekly/{year}-W{week} 周报与收尾报数。分母为 0 只写「本期无单」，不出百分比、不编数据；整改回执源未接入时如实标注源缺口，不推断状态。

## 智能体

- 巡店岗
- 整改岗
- 复盘岗

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-memory** — 端点 `https://mcp.finddatatech.cloud/fd-memory/mcp`
- **fleet** — 端点 `https://mcp.finddatatech.cloud/fleet/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
