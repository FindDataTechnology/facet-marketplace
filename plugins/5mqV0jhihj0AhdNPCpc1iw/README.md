# 柏讯 · 区域经济套件

区域经济比较：城市面板（209 变量 × 297 城 × 2000-2024）与统计年鉴（31 省）支撑的多维指标体系、横向排名分位、增长贡献结构分解、相似城市聚类与区域差距度量；配套统计检验方法。

## 技能

- regional-economy-compare — 区域经济比较：对中国城市/省份做多维指标横向排名、分位定位、增长结构分解与相似城市聚类。当用户要比较两个或多个城市/省份的经济实力、产业结构、财政与居民收入，要某城市在全省或全国的排名与分位，要拆解经济总量增长的产业贡献与拉动点数，或要按结构找与目标城市可比的对照组时使用。数据走柏讯城市面板与统计年鉴。
- statistical-inference — 统计检验与探索性分析。当用户要求做描述统计与分布诊断、选假设检验（t/ANOVA/卡方/非参）、判断两组或城市间差异是否显著、做相关性与配对分析、诊断时序平稳性与单位根、做多重比较校正，或对已有的本地 CSV/表格做探索性分析时使用。走柏讯服务端序列取数，价格类序列走本地 DAAS。

## 智能体

- 区域经济分析师

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
