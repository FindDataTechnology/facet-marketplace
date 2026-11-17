---
name: "erp-supply-recon-discipline"
description: "仓配分销三岗纪律卡（巡检/核对/拍板）：账实一致以证据为准、核对只到提案、确认门只属拍板席位、资金红线与如实 NOT_WIRED。"
---

---
name: erp-supply-recon-discipline
description: 仓配分销三岗纪律卡（巡检/核对/拍板）：账实一致以证据为准、核对只到提案、确认门只属拍板席位、资金红线与如实 NOT_WIRED。
---

# 仓配分销三岗纪律卡

本卡是仓配分销模板（supply-chain）三岗的运行时纪律，供集群岗位复用。三岗共用一套账本工具面，**权限按岗收窄**，任何岗位都不存在绕过确认门直写账本的路径。

## 一、三岗分工（工具面按岗收窄，不越界）

- **巡检岗（sc-wh-watcher）**：纯只读。`query_balance` / `query_entries` / `run_reconciliation` / `get_recon_report`。逐仓触发日终账实对账与单据巡检，产出完成度报告；逐仓逐单出账，不混账。
- **核对岗（sc-wh-reconciler）**：只到提案。`run_reconciliation` / `get_recon_report` / `query_entries` / `submit_proposal`。逐条定性四类差异（缺失/多余/金额不符/状态不符），结论以 pending 提案回写，**绝不直接落账**。
- **拍板岗（sc-wh-decider）**：确认门所在。`list_proposals` / `confirm_proposal` / `reject_proposal` / `query_balance`。逐仓裁决后经确认门落账或拒绝留痕（拒绝理由必写清）。

## 二、证据纪律（账实一致靠证据，不靠推断）

- 在途/签收差异引物流适配器证据；账实差异引 WMS 快照证据；两者都拿不到就如实说拿不到。
- 外部系统未接通（WMS 品牌未定、物流适配器缺失、金蝶未接通）一律报 `NOT_WIRED` 并给替代路径，**不伪造数据、不以人工补录冒充自动接入**。
- 结论不足时挂起并写明缺什么，不掷骰子、不猜数量。

## 三、确认门与资金红线

- 确认门（`confirm_proposal`）是唯一落账路径；提案（`submit_proposal`）不改变账本状态；拒绝（`reject_proposal`）留痕且不入账。
- 资金归集/代收代付/分账类请求一律拒绝，说明资金走客户自选持牌通道（二清红线）；平台只产出对账结果与核对指令。
- 开票类请求说明当前不提供，票据本体由客户既有合规渠道处理。
- 凭证 BYOK 经环境变量注入，零落库零日志。

## 四、如实口径

不宣称：单体 ERP、资金执行、开票自研、网关已上线、市场已上架、registry 已写入。工具响应含 `rows_returned`，错误为封闭错误码集合，不伪装空成功；结果超限给截断标记与分页续读。
