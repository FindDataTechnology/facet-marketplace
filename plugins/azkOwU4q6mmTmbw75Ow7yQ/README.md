# 柏讯 · 个股投研套件

个股深度投研：从取数与口径、同行与竞争、财务建模、论点综合到 13 章报告装配的完整链条。五技能对齐 FinRobot 生产版 V2 的五个在跑角色（数据/分析/建模/综合/报告）。财务与披露走柏讯服务端通道 A，价格与市值走本地通道 B，估值与统计数字一律由 fd-quant 计算面产出（不自行计算）。与「估值套件」（单点估值）、「财务底盘」套件的分工：本套件是**全链报告**，估值套件是**单点深挖**，财务底盘是**通用财务分析**。方法分歧时点位可缺（null），评级必发。

## 技能

- equity-research-data — 个股投研的取数与口径纪律层。当用户要求为某家 A 股/港股公司取财务科目、公告披露、行业财务指标或公司面板变量，或需要交代某个数字的来源工具、数据期数、币种与单位时使用。财务与披露一律走柏讯服务端通道 A，价格与市值一律走本地 DAAS 通道 B，两条通道不混用、不互相替代；取不到的数值如实声明缺口，不得填补。
- equity-research-peers — 个股投研的同业与竞争层。当用户要求为某家 A 股/港股公司挑可比公司、给同业规模与增速带对照、做可比性说明或跑倍数对照表时使用。候选池用 GTA 公司面板按规模带与增速带筛选（通道 A），价格类输入与倍数只走本地 DAAS（通道 B）；同业之间的可比性差异必须逐项标注，不得抹平。
- equity-research-modeling — 个股投研的估值建模与计算层。当用户要求给某家 A 股/港股公司做 DCF、DDM、可比倍数、Monte Carlo 或敏感性分析，或要求算基本面比率、跑红旗检测、把多种估值方法合成目标价时使用。全部算术一律调 fd-quant 确定性计算面（通道 Q），本技能只组织输入、标注来源、原样转述返回；计算面不可用时顶部明示"估值部分未完成"，绝不自算顶替。财务科目走柏讯服务端通道 A，价格与市值类输入走本地 DAAS 通道 B。
- equity-research-thesis — 个股论点综合与方向判定（Rating 契约实例）。当用户要求给某只股票下方向结论、写投资论点、列催化剂与风险、给目标价，或把取数/同行/建模三段结果收敛成一个可入决策账本的判定时使用。产出 side / conviction / price_target（可 null）/ basis / evidence；方法分歧、价格通道不可用或输入不足时扣值不扣结论。财务与披露走柏讯服务端通道 A，本地库存读数走本地 DAAS 通道 B，估值与统计量走 fd-quant 计算面通道 Q。
- equity-research-report — 个股投研报告装配（本套件入口技能）。当用户要求"给某只股票出一份完整研究报告""写一份研报""跑一遍个股投研流程"时从这里进入。按固定 13 章装配报告、执行六项叙述审计自检、保留缺数据章并写明原因、末尾给数据期数汇总行。串起取数与口径、同行比较、财务建模叙述、论点综合四段子流程，产出 13 章报告与一个 Rating 契约实例。

## 智能体

- 柏讯 · 个股投研助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
