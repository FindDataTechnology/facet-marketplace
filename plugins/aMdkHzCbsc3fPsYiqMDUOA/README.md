# 柏讯 · 舆情驱动

柏讯舆情驱动三件套：把公告文本变成带原文引用、可核验、可追踪的观察维度——公告舆情审读（sentiment-document-review，逐篇打分必带引用与字符定位、多篇给分布而非平均情绪、服务拒答如实转述、低置信显式标注）、舆情转向追踪（sentiment-trend-watch，按公告发布日排布、转向点必须附对应文档、样本门槛单期 ≥3 篇且高置信 ≥2 篇、覆盖面与置信度固定两栏）、舆情与价格持仓的描述性对照（sentiment-signal-bridge，只陈述时间先后不断言因果、相关性陈述须 ≥12 对且走计算面、严禁给买卖建议与「信号」称谓）。与投研套件的分工：研报的催化剂章引用本套件分数，不重复打分（同一服务同一幂等键，重复调用命中缓存）；与策略工坊的分工：那边做信号与回测，本套件只做描述性对照不给信号。分数不预示价格、当前版本（gold-v10 / prompt v7）两轮复测置信桶单调递增、方向一致率 99.19%，但五档精档之间仍有相邻档误判，均如实标注。

## 技能

- sentiment-document-review — 柏讯舆情驱动 · 公告舆情审读。当用户要求"看看这家公司最近的公告口径怎么样""给这几篇公告做个舆情判断""这些公告偏正面还是负面""公告里有什么值得注意的表述"时使用。取公告全文（巨潮面）→ 逐篇打分（fd-sentiment，分数必带原文引用）→ 呈现分数/band/置信度/引用片段与定位。多篇给分布而非平均情绪；拒答时如实转述；低置信显式标注。不做交易信号，不给情绪指数。
- sentiment-trend-watch — 柏讯舆情驱动 · 舆情转向追踪。当用户要求"这家公司最近舆情是在变好还是变差""这段时间公告口径有没有转向""按月看公告情绪的分布变化""找出情绪转折点对应的公告"时使用。按公告发布日排布分数，给逐期分布与转向点（附对应文档），并强制报告覆盖面与置信度两栏。样本不足时明示不下转向结论。不给情绪指数、不给交易信号。
- sentiment-signal-bridge — 柏讯舆情驱动 · 舆情与价格/持仓的描述性对照。当用户要求"舆情和股价是什么关系""情绪是不是领先价格""看看公告情绪和我持仓的对应""舆情能不能当信号用"时使用。做的是描述性对照：把分数分布的时间位置与价格/持仓的时间位置并排放，说清先后与是否同期，并强制标注样本是否足以做描述性陈述。严禁给买卖建议、严禁把舆情称为信号。价格通道或账本不可用时明示降级。

## 智能体

- 柏讯 · 舆情助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-sentiment** — 端点 `https://mcp.finddatatech.cloud/fd-sentiment/mcp`（需要组：wire-customers）
- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
