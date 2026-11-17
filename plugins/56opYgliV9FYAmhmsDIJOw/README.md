# 晨报情报站

官方团队模板：每日晨报情报站——科技与市场情报员每日清晨分头巡检，情报主编 8 点合成晨报；另含团队引用岗（existing-cluster，每周一做深度聚合，演示团队 fan-out 引用与派生报数）。演示：双情报源并行、晨报流、cluster 引用岗（公司种子只演示了 a2a）；编导段为 trigger+handoff 链（两情报员巡检完成接棒主编合成）与团队 report 上行。铸造上架时 packId/agents 按铸造脚本补齐；本文件是清单真源，测试钉住其必须通过 fleet 段预校验。

## 技能

- intel-item-five-elements — 情报员采集与初筛的标准格式：事实、信源、影响面、置信度、建议动作。
- brief-synthesis-template — 主编合成晨报的标准结构：头条、要点、风险预警、信源覆盖度。

## 智能体

- 情报主编 — 每日晨报合成
- 科技情报员 — 科技面每日巡检
- 市场情报员 — 行情异动每日巡检

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
