# 蓝图构造师

蓝图构造师：三岗共编的构造面——架构师把自然语言垂直描述编译成蓝图草稿（板块边界/数据来源/团队编制/看板/监控/验收 + 内联岗位 + 工具面），技能作者补技能桶正文，装配岗补三支柱（记忆章程 / 逐岗工具面 / 看板接线）；三岗产出同一份「团队发布」，过全链校验后呈现 diff，停等主人批准；上架经 propose_action 提案 → 拍板队列 → 已批准执行通道落地；**提审只覆盖「上架」**（动作闸无建队动作），建队由主人在驾驶舱装包、新队生来 paused。构造师工具面不含发布与实例化写工具——「未批准不落库」由结构保证，不靠提示词。

## 技能

- harness-constructor — 垂直蓝图编制纪律（架构师岗）：NL→九段草稿（形状自检+fleet_draft+包级干跑）→diff→停等批准→移交装配岗提审；技能桶交技能作者岗补。
- skill-authoring-discipline — 技能正文编制纪律（技能作者岗）：复用优先、三要件齐备、上限与红线自检、收口 diff 停等批准。

## 智能体

- 架构师
- 技能作者
- 装配岗

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **facet-market-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-market-mcp/mcp`
- **blueprint-author** — 端点 `https://mcp.finddatatech.cloud/blueprint-author/mcp`
- **actions** — 端点 `https://mcp.finddatatech.cloud/actions/mcp`
- **pack-author** — 端点 `https://mcp.finddatatech.cloud/pack-author/mcp`
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
