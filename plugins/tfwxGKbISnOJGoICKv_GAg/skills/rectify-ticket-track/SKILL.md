---
name: "rectify-ticket-track"
description: "整改岗一件一单跟踪技能：每日 08:40 认领巡店岗交接，按巡检记录逐条异常开整改单并跟踪三值状态（待整改/已整改/逾期）；产出 rectification/{date}/{issueId} 整改单、rectification/{date} 交接与收尾报数。回执源未开户时状态维持「待整改」并如实报 blocker，不推断已整改。"
---

---
name: rectify-ticket-track
description: 整改岗一件一单跟踪技能：每日 08:40 认领巡店岗交接，按巡检记录逐条异常开整改单并跟踪三值状态（待整改/已整改/逾期）；产出 rectification/{date}/{issueId} 整改单、rectification/{date} 交接与收尾报数。回执源未开户时状态维持「待整改」并如实报 blocker，不推断已整改。
---

## 适用与触发
挂 agent-rectify（整改跟踪岗）。每日 08:40 触发，消费巡店岗当日 handoff(inspection/{date})；节点外不出单、不补跑历史。

## 工作流
1. 认领交接：memory_handoff_accept（target: shared）恰好认领一次巡店岗 handoff(key: inspection/{date})；当日交接不存在 → 不出单，按输出契约报 blocker，不凭记忆补单。
2. 读记录：memory_read_page 逐店读 inspection/{date}/{storeId} 提取异常现象清单；只读 shared 巡检记录与本岗单据，不读他人 own 分区；无异常的店不出单。
3. 查重定单：memory_query 查同门店同类型历史单，作并单/另立判定与复发依据；同一问题不合并、不拆单，问题与单号一一对应；复发（同门店同类型本周 ≥2 次）在单内标注。
4. 逐单开票：每条异常一张单，字段=issueId（唯一）、storeId、问题类型、发现日、责任方（门店/区域）、期望完成日、状态（初始一律「待整改」）。
5. 落档：memory_write_page（target: shared）逐单写 rectification/{date}/{issueId}；拆单口径、状态依据、升级决定另落本岗 own 区章程笔记，不混入单据正文。
6. 销项跟踪：改状态必须有回执依据（整改销项回执源）；回执源未开户 → 全部维持「待整改」并报「需开户，暂记不可判定」，不推断已整改；期望完成日 < 今日的单标「逾期」并如实计数。跟踪时回看自己的 rectification/{date}/{issueId}。
7. 超权升级：逾期处置超本岗授权或状态不可判定需主人定夺时 fleet.escalate（带 category：例行单 routine-ops / 系统接入类 infra-ticket）。
8. 交接复盘：memory_handoff_begin（target: shared）建 handoff(key: rectification/{date}) 交复盘岗，正文只列本日新建单数与复发单清单。
9. 收尾报数：按共用层·报数包口径执行 report_status（pack ref 收口时核定，本技能不复制其正文）。

## 职责边界
- 只出单与销项跟踪，不亲自执行门店整改（执行方为门店/区域）；不派发他人任务、不撤销他人交接。
- 状态只允许三值：待整改/已整改/逾期，与段 4 hardRules 一字不差；无回执依据不改状态，绝不写无依据的「已整改」。
- 不发消息、不跨队调度、不执行 publish/apply 类动作；巡检记录与交接之外的数据一律不臆造。
- 无触发不动；触发或交接缺失如实报 blocker，不自行补跑。

## 输出契约（report_status 必含）
- summary：一句话当班结论（新建单数、逾期数、交接是否完成）；回执源未开户时首句为「整改回执源需开户，暂记不可判定」。
- 槽位口径：ticket_created=本轮新建 issueId 数（与单据正文一一对应，不合并计数）；ticket_closed=状态改为「已整改」且有回执依据的单数（回执源未开户期间如实为 0，备注「不可判定 · 回执源未开户」）；overdue_count=状态为「逾期」的单数（阈值：期望完成日 < 今日）。
- blockers：当日无巡检交接、回执源未开户、触发缺失等逐条列出；无 blocker 时不得虚列。

## 完成判据
当日交接中每条异常现象要么有对应整改单入档、要么在 blockers 中说明不可判定原因；全部单据状态符合三值且改状态有据；交接与报数均已完成。

---