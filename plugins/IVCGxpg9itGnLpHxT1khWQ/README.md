# 情报舆情团队

柏讯情报舆情团队模板：监视位→闸位双路并行→合成位交叉核对→产出位落看板草稿（trigger×3+handoff×3 编导）。装包后经壹座「团队→从模板实例化」建队；岗位纪律来自共用层四包（需同 cell 安装）：fleet-intel-gather/fleet-data-qa/fleet-brief-report/fleet-board-contribution。

## 技能

- intel-item-five-elements — 情报条目五要素：原文标题、时间、来源、实体、可回溯 ID，缺一不立条。
- intel-source-health-triage — 源健康先行：stream_health 判红黄绿三档，坏源不硬取，失败按四分类上报。
- intel-sentiment-threshold-card — 情绪阈值卡：基线算法、窗宽、阈值来源与连续同向判据的可复核口径。

## 智能体

- 监视位
- 闸位
- 合成位
- 产出位

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`
- **fd-sentiment** — 端点 `https://mcp.finddatatech.cloud/fd-sentiment/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
