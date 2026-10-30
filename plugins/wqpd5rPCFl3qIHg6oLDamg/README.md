# 柏讯 · 决策复盘套件

把「当时的判定」变成可审计的历史：决策落账（追加式、幂等、修订留痕）→ 到期按基准结算真实 alpha → 提炼反思（只注入决策者，不广播）→ 持仓体检与最近判定的一致性核查。以账本为准，不美化历史；样本不足不下结论。与决策类 pack 的分工：决策 pack 产出 Rating/TargetWeights 实例，本套件负责统一落账与复盘（语义相同，不重复实现）。

## 技能

- decision-review-log — 柏讯决策落账纪律（复盘套件入口）。当用户要求把一条判定（Rating）或目标权重（TargetWeights）记入决策账本、要求"再记一次"、要求修改或"更正"已落账的判定、或需要查回某标的的历史判定与教训时使用。账本是追加式的：幂等键 (caller, as_of, symbol, pack, skill) 拒绝重复提交并返回既有 decision_id；记录按 caller 隔离，只看到自己的判定，修订产生新记录并以 revises 指向被修订记录，历史判定永不被就地改写。
- decision-review-settlement — 柏讯到期结算复盘。当用户要求结算某条已落账的判定、想知道某判定"后来怎么样了"、要求汇总待结算记录、或要看原始收益与相对基准 alpha 时使用。流程为待结算汇总 → 取结算窗序列（价格走本地 DAAS 通道 B）→ 触发账本结算 → 三段式输出（判定理由回顾 + 结算结果 + 对照评述）。持有期未满不强行结算；alpha 为负不等于判断错误。
- decision-review-reflection — 柏讯决策反思提炼。当用户要求为一条已结算（或已落账）的判定写复盘反思、要求"总结这次教训"、或询问为什么反思不该出现在其他角色的输出里时使用。反思固定 2–4 句，针对决策本身而非市场走势，区分"判断错"与"运气差"，并含可执行的改进项；反思由账本存储、只注入最终决策者的上下文，不得广播给其他角色，缺失反思不阻塞结算。
- decision-review-portfolio — 柏讯持仓体检。当用户要求体检当前模拟账户持仓、看集中度（HHI/前 N 权重）、期间回撤、行业暴露，或核查"持仓是否偏离了最近一次判定"时使用。持仓与账户取自柏讯模拟交易服务（paper-only，模拟盘，无真实资金），统计量一律交给 fd-quant 计算面，缺失数据如实标注，结论为研究参考、不含买卖建议。

## 智能体

- 柏讯 · 决策复盘助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
