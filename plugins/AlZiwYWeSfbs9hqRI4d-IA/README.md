# 柏讯 · 估值套件

股票估值与财报周期分析：DCF（五年预测 + WACC + 终值 + 敏感性表）+ 可比公司 + 三表联动三角验证；财报前瞻与复盘（科目定位与质量分析）。财务数据走柏讯服务端，股价与市值走本地数据平台通道。

## 技能

- equity-valuation — 股票估值（DCF + 可比公司 + 三表联动）。当用户要求给某家 A 股/港股公司做内在价值或相对估值、算 WACC 与终值、跑敏感性与情景表、给目标价区间、做同业倍数对照与反推市场隐含预期时使用。财务数据走柏讯服务端通道 A，价格与市值走本地 DAAS 通道 B；通道 B 未连接时中止估值，不给目标价。
- earnings-cycle-analysis — 财报周期分析（前瞻 + 复盘）。当用户要求对一家公司做财报前瞻（本次披露类型与时间、该盯哪些科目、超预期/低于预期可能从哪来）、做财报复盘（实际 vs 上年同期、差异由哪个科目驱动、盈利质量如何、对后续季度意味着什么），或梳理连续数期经营动能变化时使用。基于柏讯财报与公告数据，不做技术分析。触发词：财报前瞻、业绩预告、财报复盘、业绩点评、超预期、低于预期、盈利质量、某某公司季报/半年报/年报分析。
- stock-research-workflow — 个股研究全流程工作流（数据-股票包入口技能）。当用户询问一只股票（A股或美股）的投资价值、要求个股分析、财报解读、估值判断或多空辩论时使用。驱动从行情基本面到估值结论的完整研究流程，调用行情工具、数据 MCP 与研究技能。

## 智能体

- 股票估值分析师

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
