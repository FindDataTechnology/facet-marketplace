# 识律 · 合同审查套件

中国法（大陆）合同审查：五张行业审查卡（劳动用工 / 借贷融资 / 保密NDA / 买卖供货 / 服务与SaaS）× 法条核验与效力锚定 × law-bench 条款装配与三重评审，产出双轴风险问题清单、审查备忘录与**修订对比表**（原条款/修订后条款/修订理由与核验依据三列，v2 新增交付形态）。

## 技能

- contract-review-cn — 中国法合同审查。判定合同适用中国大陆法后，加载通用原则与五张行业审查卡（劳动/借贷/保密/买卖/SaaS），逐条审查并产出双轴风险问题清单与审查备忘录；涉精确法条、阈值、生效日期时经法律数据工具核验。当用户要求审查/审阅/markup 合同、检查条款、问某条款在中国法下是否有效或如何修改时使用。仅覆盖中国大陆法，不含港澳台、不含刑事与行政、不含外国法规则包。

## 智能体

- 识律 · 合同审查顾问

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-legal-search-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-legal-search-mcp/mcp`（需要组：legal）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：legal）
- **law-bench** — 端点 `https://mcp.finddatatech.cloud/law-bench/mcp`（需要组：legal）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
