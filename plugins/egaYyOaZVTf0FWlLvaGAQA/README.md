# 柏讯 · 投研三镜头

三员投研团队：宏观分析（环境）、估值分析（价值）、投委会多空质询（博弈）三个独立镜头审读同一问题。团队任务下扇出聚合；单聊可切换任一角色。舆情/风控等更多角色可入动态团队叠加。

## 技能

- macro-cycle-analysis — 宏观周期与景气分析。当用户要求判断当前经济周期位置（复苏/过热/滞胀/衰退）、做六域景气扫描（增长/通胀/货币信用/财政/外贸/地产消费）、做领先-同步-滞后指标分层、识别景气拐点、梳理宏观变量到其业务问题的传导链，或做省际补充与国际对比时使用。走柏讯服务端宏观数据，不含收益率曲线与互换分析。
- statistical-inference — 统计检验与探索性分析。当用户要求做描述统计与分布诊断、选假设检验（t/ANOVA/卡方/非参）、判断两组或城市间差异是否显著、做相关性与配对分析、诊断时序平稳性与单位根、做多重比较校正，或对已有的本地 CSV/表格做探索性分析时使用。走柏讯服务端序列取数，价格类序列走本地 DAAS。
- equity-valuation — 股票估值（DCF + 可比公司 + 三表联动）。当用户要求给某家 A 股/港股公司做内在价值或相对估值、算 WACC 与终值、跑敏感性与情景表、给目标价区间、做同业倍数对照与反推市场隐含预期时使用。财务数据走柏讯服务端通道 A，价格与市值走本地 DAAS 通道 B；通道 B 未连接时中止估值，不给目标价。
- earnings-cycle-analysis — 财报周期分析（前瞻 + 复盘）。当用户要求对一家公司做财报前瞻（本次披露类型与时间、该盯哪些科目、超预期/低于预期可能从哪来）、做财报复盘（实际 vs 上年同期、差异由哪个科目驱动、盈利质量如何、对后续季度意味着什么），或梳理连续数期经营动能变化时使用。基于柏讯财报与公告数据，不做技术分析。触发词：财报前瞻、业绩预告、财报复盘、业绩点评、超预期、低于预期、盈利质量、某某公司季报/半年报/年报分析。
- ic-adjudication — 柏讯投委会 · 裁决（入口技能）。当用户要求"开投委会""对某只股票做一次多空辩论并给结论""裁决多头空头的分歧""把辩论结论落账"时使用。本技能是套件入口：调度 ic-bull-case 与 ic-bear-case 双方立论，然后按四条裁决纪律给出 Rating 契约实例（冲突不给 hold 须指强弱、证据不足给 review 不降级、裁决不引用任何数字、change_my_mind 必填），并完成落账（账本不可用时明示"未落账"）。单 agent 分角色形态，不构成独立视角。
- ic-bull-case — 柏讯投委会 · 多头立论。当用户要求对某只股票构建看多论证、写多头观点、准备投委会辩论的多方意见、把证据集收敛成"为什么该买"的最强论证，或作为 ic-adjudication 的立论阶段被调度时使用。本技能不取数、不计算、不裁决——数字只能引用证据集的 evidence_id，论证组织是唯一职责。与 ic-bear-case 对称，空头未发言时不得替对手立论。
- ic-bear-case — 柏讯投委会 · 空头立论（辩论三角色之一，与 ic-bull-case 完全对称）。当用户在投委会辩论流程中需要空头方论证、问「有什么理由不买」「多头论证哪里站不住」「估值凭什么下修」，或作为 ic-adjudication 的立论阶段被调度时使用。本技能不取数、不计算、不裁决——数字只能引用证据集的 evidence_id；不同意多头时只能提出新证据或指出其适用边界，不得改写数字。空头论证为空是合法产出，不得为对称而编造。

## 智能体

- 宏观经济研究员
- 股票估值分析师
- 柏讯 · 投委会主持人

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
