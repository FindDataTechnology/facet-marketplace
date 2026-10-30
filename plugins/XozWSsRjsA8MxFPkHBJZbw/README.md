# 柏讯 · 行业与企业套件

行业景气与产业链传导 + 企业基本面与财务质量 + 面板计量方法：行业格局与上下游传导落到上市公司实证（GTA 面板 1,468 变量），企业端做杜邦分解与财务红旗项识别，计量侧支持固定效应、聚类稳健标准误与双重差分。

## 技能

- industry-chain-analysis — 行业景气与产业链传导分析。当用户要判断一个行业处在景气周期的哪个位置、梳理上下游投入产出与成本价格传导路径、用上市公司财务面板实证行业判断、刻画行业格局（规模/增速/集中度/进入壁垒），或把研报观点与公司真实财务数据对照时使用。走柏讯服务端行业与公司财务数据，不涉及价格与市值分析。
- corporate-fundamental-analysis — 企业基本面与财务质量分析（柏讯线）。当用户要求给一家 A 股上市公司做三表速览、杜邦分解、现金流质量诊断、财务红旗项排查或同业分位对照时使用。走 GTA firm-year 财务面板 + 中国财报三表与附注，输出带来源与数据期数的可复核结论。本技能只做基本面质量判断，不做估值、不给目标价。
- panel-econometrics — 面板数据计量分析。当用户要在"个体 × 时间"二维结构上做推断时使用——固定效应/随机效应与 Hausman 检验、聚类稳健标准误、动态面板（滞后因变量）、双重差分 DID 与平行趋势/事件研究检验、断点回归概念。面板取自柏讯服务端三类源：GTA 上市公司（firm-year）、中国城市（city-year）、世界银行 WDI（country-year），宏观控制变量取柏讯宏观序列。不涉及股价与市值建模。

## 智能体

- 行业与公司研究员

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
