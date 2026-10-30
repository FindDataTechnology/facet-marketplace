# 谦面工坊

生态创作者的铸包工坊：把一个想法做成可安装的功能集（pack）。①铸包——发现平台可用的 MCP 工具与技能、查重、组装 manifest、预校验、发布；②改编——用开源或外部技能打底时的许可证判定与署名（含降级路径与防编造约束）；③验收——发布后自查：装得上吗、工具真的被调用了吗、取不到时说得诚实吗。面向生态创作者；发布需创作者身份。

## 技能

- facet-forge-assembly — 在谦面把想法铸成功能集（pack）：先发现可用的 MCP 工具与技能，再组装 manifest（内联技能 + MCP 引用 + 角色），预校验通过后发布。用户要「做一个功能集/预设套件/上架」「把某能力打包给用户装」「我有个想法想做成 AI 助手」时使用。
- facet-forge-adaptation — 把别人的技能（GitHub 开源技能、外部技能市场条目、谦面已上架 pack 里的技能）改编成自己的功能集内容时的合规与改编方法：许可证能不能用、怎么署名、怎么把数据环节换成平台工具。用户说"照着这个开源项目做一个""把这个技能抄过来改改""参考某某套件"时使用。
- facet-forge-acceptance — 功能集发布之后的自查清单：确认用户装得上、装上了能用、工具真的被调用过、工具说做不到时是否诚实。用户说"我发布了这个功能集，接下来呢""怎么知道它有没有用""用户反馈装不上/用不了"时使用。

## 智能体

- 铸造师

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **airegistry-tools** — 端点 `https://mcp.finddatatech.cloud/airegistry-tools/mcp`
- **facet-market-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-market-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
