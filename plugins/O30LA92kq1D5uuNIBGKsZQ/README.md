# 能力包·构造·蓝图构造师

harness L3 构造面：把自然语言垂直描述编译成蓝图草稿（板块边界/数据源/集群编制/看板骨架/监控/验收口径），复用既有校验器 dry-run，diff 呈现后停等主人批准——批准才铸造上架与实例化。零新平台能力：只引用 fleet-architect 与 facet-market 既有面。蓝图格式见 fleet-packs/blueprints/schema.md。

## 技能

- harness-constructor — 垂直蓝图编制纪律：NL→草稿→既有校验器→diff→停等批准→批准后铸造；字段契约与八舰同源。

## 智能体

- 垂直构造师

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **facet-market** — 端点 `https://mcp.finddatatech.cloud/facet-market/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
