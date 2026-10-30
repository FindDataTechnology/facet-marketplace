# 柏讯 · 投委会

柏讯投委会三件套：把 TradingAgents/FinRobot 写好但未接线的公司层级辩论程序做成可安装的模式——多头立论（ic-bull-case，数字只能引用证据集 evidence_id、不自造不估算、不替对手立论、把己方理由说尽）、空头立论（ic-bear-case，对称规则 + 不扭曲多头已陈述证据）、裁决（ic-adjudication，入口技能：调度双方后按四纪律给出 Rating 契约实例——冲突不给 hold 须指强弱、证据不足给 review 不降级、裁决不引用任何数字、change_my_mind 必填），并落账至决策账本（结论可被 P5 结算——这是上游两家都缺失的闭环）。诚实定位：单 agent 分角色形态，三个角色由同一模型扮演，不构成独立视角；升级形态（萬星 A2A 三 persona 串行委派，深度 3 / 并发 1 / 分钟计费）已写入技能文本属产品演进。辩论不宣称提高预测准确率，是否更准由账本结算回答。裁决为研究参考，不构成投资建议。

## 技能

- ic-adjudication — 柏讯投委会 · 裁决（入口技能）。当用户要求"开投委会""对某只股票做一次多空辩论并给结论""裁决多头空头的分歧""把辩论结论落账"时使用。本技能是套件入口：调度 ic-bull-case 与 ic-bear-case 双方立论，然后按四条裁决纪律给出 Rating 契约实例（冲突不给 hold 须指强弱、证据不足给 review 不降级、裁决不引用任何数字、change_my_mind 必填），并完成落账（账本不可用时明示"未落账"）。单 agent 分角色形态，不构成独立视角。
- ic-bull-case — 柏讯投委会 · 多头立论。当用户要求对某只股票构建看多论证、写多头观点、准备投委会辩论的多方意见、把证据集收敛成"为什么该买"的最强论证，或作为 ic-adjudication 的立论阶段被调度时使用。本技能不取数、不计算、不裁决——数字只能引用证据集的 evidence_id，论证组织是唯一职责。与 ic-bear-case 对称，空头未发言时不得替对手立论。
- ic-bear-case — 柏讯投委会 · 空头立论（辩论三角色之一，与 ic-bull-case 完全对称）。当用户在投委会辩论流程中需要空头方论证、问「有什么理由不买」「多头论证哪里站不住」「估值凭什么下修」，或作为 ic-adjudication 的立论阶段被调度时使用。本技能不取数、不计算、不裁决——数字只能引用证据集的 evidence_id；不同意多头时只能提出新证据或指出其适用边界，不得改写数字。空头论证为空是合法产出，不得为对称而编造。

## 智能体

- 柏讯 · 投委会主持人

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
