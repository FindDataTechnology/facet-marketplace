---
name: "store-inspect-record"
description: "巡店岗逐店巡检记录技能：每日 08:10 触发后按门店清单逐店记录营业状态/陈列/缺货，事实清单体、只记录不改动；产出 inspection/{date}/{storeId} 逐店记录、inspection/{date} 交接与收尾报数。门店台账未开户时如实报 blocker，不编造门店。"
---

---
name: store-inspect-record
description: 巡店岗逐店巡检记录技能：每日 08:10 触发后按门店清单逐店记录营业状态/陈列/缺货，事实清单体、只记录不改动；产出 inspection/{date}/{storeId} 逐店记录、inspection/{date} 交接与收尾报数。门店台账未开户时如实报 blocker，不编造门店。
---

## 适用与触发
挂 agent-inspect（巡店记录岗）。每日 08:10 触发；节点外不巡检、不补跑历史。

## 工作流
1. 取清单：读门店基础台账取本次巡检门店全集与营业/停业/歇业状态。台账未开户或源不可用 → 不巡检，按输出契约报「需开户，暂记不可判定」，绝不编造门店名或店号。
2. 去重：memory_read_page 读当日 inspection/{date} 已有记录，已入档门店不再重复巡检。
3. 逐店记录：每店一条，字段=storeId、记录时间、营业状态（枚举：营业/停业/歇业）、陈列现象、缺货现象。异常只描述现象，不判根因、不给整改建议。
4. 入档校验：storeId 与记录时间缺一不入档；营业状态不在枚举内写「无法判定」，不猜。
5. 落档：memory_write_page（target: shared）逐店写 inspection/{date}/{storeId}；只写本岗当日 inspection 键，不改他人分区。
6. 交接：memory_handoff_begin（target: shared）建 handoff(key: inspection/{date}) 交整改岗，正文只列覆盖店数与异常店清单。
7. 收尾报数：按共用层·报数包口径执行 report_status（pack ref 收口时核定，本技能不复制其正文）。

## 职责边界
- 只记录不改动：不调陈列、不改价、不下单、不承诺整改时限；不判根因、不派整改单（归整改岗）。
- 不发消息、不跨队调度、不执行 publish/apply 类动作。
- 门店台账源需开户：未开户期间该步如实记「需开户，暂记不可判定」，槽位不虚报。
- 无触发不动；触发缺失如实报 blocker，不自行补跑。

## 输出契约（report_status 必含）
- summary：一句话当班结论（覆盖店数、异常店数、交接是否完成）；台账未开户时首句为「门店台账需开户，暂记不可判定」。
- 槽位口径：inspected_count=实际入档记录条数（非清单全集）；issue_count=出现异常现象的门店数（一店多项异常计 1）；handoff_ready=inspection/{date} 交接已建与否（是/否）。
- blockers：门店台账未开户、触发缺失等逐条列出；无 blocker 时不得虚列。

## 完成判据
本日每家应巡门店要么有入档记录、要么在 blockers 中说明不可判定原因；交接与报数均已完成。

---