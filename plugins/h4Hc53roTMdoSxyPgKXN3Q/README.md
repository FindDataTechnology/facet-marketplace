# 跨境电商物流值守团队

跨境电商物流值守团队模板：值守岗读 fd-open-data-mcp 口岸观测（port.calls 当日快照，四枢纽港逐港报水位）→合成位按三判据出物流日报（超阈/断更/政策面冲突 escalate）→产出位落看板草稿。装包后经壹座「团队→从模板实例化」建队；岗位纪律来自共用层三包+值守岗包 crossborder-watch-roles（同 cell 安装）。边界：政策公告面归政策盯梢舰，本舰只管口岸水位。

## 技能

- xb-port-call-pressure-reading — 口岸水位读数纪律：port.calls 当日快照不回填，缺港按 series_state 双态归因，拥堵与吞吐分列不混算。

## 智能体

- 监视位 — 逐港读 port.calls 当日口岸观测水位并报异动
- 合成位 — 交叉核对报数合成物流日报，按三判据 escalate
- 产出位 — 物流日报落看板草稿，不发布

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

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
