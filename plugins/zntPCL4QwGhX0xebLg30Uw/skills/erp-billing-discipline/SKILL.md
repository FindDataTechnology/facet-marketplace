---
name: "erp-billing-discipline"
description: "计费三岗纪律：规则版本化且人工审批上线、费率必须有出处、重算幂等、红冲保留原行、确认门只属拍板岗、资金红线。"
---

---
name: erp-billing-discipline
description: 计费三岗纪律：规则版本化且人工审批上线、费率必须有出处、重算幂等、红冲保留原行、确认门只属拍板岗、资金红线。
---

# 计费三岗纪律卡

本卡是计费团队三岗的运行时纪律。三岗共用一套计费工具面，**权限按岗收窄**：包级 allowTools 只是封顶，真正生效的是每岗 `resources.mcpTools`。

## 一、三岗分工（工具面按岗收窄，不越界）

- **巡检岗（只读）**：`list_billing_rules` / `list_fees` / `get_fee_detail`。盘点规则版本与状态机（draft / pending_approval / active / retired）、抽检费用明细的四层溯源（费用项→业务单据→作业事件→规则版本），标出缺费率出处与快照版本不符的费用行。零写入。
- **核对岗（只出提案与草稿）**：`list_billing_rules` / `list_fees` / `get_fee_detail` / `simulate_billing` / `bill_event` / `submit_billing_rule`。用试算与幂等重推重算费用、定性差异并回写提案，把规则修订落成草稿；**没有审批门**。
- **拍板岗（唯一审批门）**：`list_billing_rules` / `approve_billing_rule` / `reverse_fee`。审批规则上线（冲突未处置拒绝上线），对错计费用开红冲。

## 二、本线纪律

- 规则版本化：同一 rule_key 只能有一条 active；升版退役旧版本，不重写历史费用。
- 费率必须有出处；无出处费率一律拒绝（RATE_UNSOURCED），不以「现行价」口头补位。
- 计费落账只走计费机器车道的提案，不绕过确认门；实收、退款、资金归集不属本团队。
- 重算幂等：同期间同事件重跑返回首次结果，快照锁定规则版本，可确定性重算。
- 红冲保留原行（append-only），新行关联被冲销原行，删除路径不存在。

## 三、确认门与资金红线

- 规则审批门（`approve_billing_rule`）只属拍板岗；核对岗提交的草稿未经审批不参与计费。
- 资金执行（资金池、代收代付、分账执行）一律拒绝——二清红线，资金走客户自选持牌通道；平台只做计费与核对，不做扣款与放款。

## 四、如实口径

取不到就报 `NOT_WIRED`，缺费率出处报 `RATE_UNSOURCED`，覆盖缺口报 `FEE_COVERAGE_MISSING`；不伪装空成功，不以零费用代替「没算」。
