# 柏讯 · 宏观研究套件

宏观周期与景气分析：六域指标框架（增长/通胀/货币信用/财政/外贸/地产消费）+ 周期定位（产出-通胀四象限）+ 领先-同步-滞后分层 + 传导链推演 + 拐点识别；配套统计检验方法做相关性与平稳性验证。数据经柏讯开放数据面与统计年鉴实时检索，每个数值标注来源与数据期数。

## 技能

- macro-cycle-analysis — 宏观周期与景气分析。当用户要求判断当前经济周期位置（复苏/过热/滞胀/衰退）、做六域景气扫描（增长/通胀/货币信用/财政/外贸/地产消费）、做领先-同步-滞后指标分层、识别景气拐点、梳理宏观变量到其业务问题的传导链，或做省际补充与国际对比时使用。走柏讯服务端宏观数据，不含收益率曲线与互换分析。
- statistical-inference — 统计检验与探索性分析。当用户要求做描述统计与分布诊断、选假设检验（t/ANOVA/卡方/非参）、判断两组或城市间差异是否显著、做相关性与配对分析、诊断时序平稳性与单位根、做多重比较校正，或对已有的本地 CSV/表格做探索性分析时使用。走柏讯服务端序列取数，价格类序列走本地 DAAS。
- china-macro-brief-workflow — 中国宏观经济分析简报工作流（数据-中国经济包入口技能）。当用户询问中国宏观经济问题（GDP、通胀、货币、社融、地产、外贸、财政、行业景气），要求宏观数据查询、趋势分析、行业对照或宏观简报时使用。驱动从指标框架到图表简报的完整流程。

## 智能体

- 宏观经济研究员

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
