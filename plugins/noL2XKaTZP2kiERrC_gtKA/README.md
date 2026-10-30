# 柏讯 · 自适应轮动套件

自适应行业轮动：从市场状态判定（慢速趋势/回撤/波动率 Z + 快速冲击）到组强度排序（申万 L1 成分股合成的行业指数）、组内排序（残差动量 + 稳健 Z + 例外框架）、风险预算建仓（产出 TargetWeights 契约实例）与闭环运行（权重模式回测 + 模拟盘 + 快照解读）。对齐 FinRL-X 的七个策略模块，但输入已按 A 股适配：波动率用已实现波动率（QVIX/iVIX 在柏讯可及环境不可达），组用申万成分股合成（官方指数序列未采），参数按实测分位重标（原 Z≥3.0 → 采用 |Z|≥2.0，因实测 3.0 只占 0.47%）。价格走通道 B（本地 DAAS，需先播种 A 股），统计一律由 fd-quant 计算面产出。与「策略工坊」的分工：工坊给通用纪律（权重语义、回测解读、风控归属），本套件给**具体策略与参数**（选哪些组、组内选谁、预算怎么分）；督导不重复实现。终产物是可执行权重与运行记录，不是买入建议。

## 技能

- rotation-regime — 柏讯自适应轮动 · 市场状态判定（套件入口）。当用户要求判断当前 A 股市场处于进攻/中性/防守状态、要求给出轮动的择时前提、或问"现在该不该加仓/降仓"时使用。产出：带证据的 risk_on / neutral / risk_off 判定 + 波动率输入说明 + 参数重标表。本技能只做判定，不选股不建仓——排序与建仓交给 rotation-ranking / rotation-portfolio。
- rotation-ranking — 柏讯自适应轮动 · 组与组内排序。当用户要求"哪些行业值得配""组内选哪几只""为什么这个组落选"时使用。组 = 申万 L1（31 类）成分股合成的行业指数；组强度用信息比排序，组内用残差动量 + 稳健 Z。产出：组排序表 + 组内排序表 + 落选原因。不建仓（交给 rotation-portfolio）。
- rotation-portfolio — 柏讯自适应轮动 · 风险预算建仓与风控参数。当用户要求"按排序结果给个仓位方案""组之间怎么分配""止损怎么设"时使用。产出：TargetWeights 契约实例（组权重 → 资产权重）+ 风险预算依据 + 风控参数（并明确止损须在策略内表达、引擎不自动执行）。
- rotation-run — 柏讯自适应轮动 · 闭环运行与解读。当用户要"把这份权重跑一下""回测结果怎么看""模拟盘为什么没成交/权重为什么偏了"时使用。产出：权重模式回测结果（含成本与基准）+ 权重快照解读 + 偏离归因。终产物是可执行权重与运行记录，不是一段买入建议。

## 智能体

- 柏讯 · 自适应轮动助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
