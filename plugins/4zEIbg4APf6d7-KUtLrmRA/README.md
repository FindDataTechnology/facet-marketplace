# 跨境电商行业监测团队

跨境电商行业监测团队模板：参谋岗每周扫外圈需求画像（观测面 TradeFlow 伙伴国分轴/FxRate/PolicyNotice 热度 + 柏讯 fd-info 需求信号定性参考）→合成位复跑验证出行业周报→产出位落看板草稿。装包后经壹座「团队→从模板实例化」建队；纪律来自共用层三包+值守岗包 crossborder-watch-roles（同 cell 安装）。边界：需求侧事实 only，内圈未授权标不可得。

## 技能

- xb-weekly-flow-scan — 外圈需求周扫纪律：TradeFlow 伙伴国分轴读、双态归因分开列、月计数注明未计入成员。

## 智能体

- 监视位 — 外圈需求画像周扫：HS6×伙伴国流向+汇率+技贸壁垒热度
- 合成位 — 合成行业监测周报并交叉核对画像
- 产出位 — 行业周报落看板草稿，不发布

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`
- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
