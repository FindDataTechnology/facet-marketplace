# 跨境电商合规分流团队

跨境电商合规分流团队模板：分流员消费政策日报/通报流/ePing 逐条出分流单（四字段，只分流不裁决）→合成位管队列三态→产出位落合规日报看板草稿。装包后经壹座「团队→从模板实例化」建队；纪律来自共用层三包+值守岗包 crossborder-watch-roles（同 cell 安装）。边界：政策巡检归政策盯梢舰，物流水位归物流值守舰。

## 技能

- xb-triage-queue-contract — 分流单四字段契约与升级三判据：缺字段标待补、监管代码单列、升级不自决定级。

## 智能体

- 监视位 — 消费政策日报与 ePing 通报，逐条分流并给出升级口径
- 合成位 — 合成合规日报队列并核对分流完整性
- 产出位 — 合规日报落看板草稿，不发布

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
