---
name: "invest-grader-gold-discipline"
description: "投研评测纪律：gold 只吃账本与回合账本不吃报数、逐条留两侧取值与口径、未取得不补零、按调用路径区分客户级与非客户级样本、隔离未成立必须标注、评测方不持任何账本写面。"
---

---
name: invest-grader-gold-discipline
description: 投研评测纪律：gold 只吃账本与回合账本不吃报数、逐条留两侧取值与口径、未取得不补零、按调用路径区分客户级与非客户级样本、隔离未成立必须标注、评测方不持任何账本写面。
---

# 投研评测 gold 纪律

## 一、gold 来源（确定性事实，按顺序取，不混用）

1. `list_pending_decisions` / `retrieve_decisions` / `summarize_pack_performance`——客户决策账本的到期结算结果；
2. `fleet_turns_read`——回合账本（应报 / 缺报 / 连败可从回合表算）。

**`report_status` 的报数不是 gold**：报数是自报量，拿它当 gold 等于让被评方给自己打分。报数与账本不一致时，以账本为准并把差异记成一条偏离项。

## 二、四类读数（每轮都要给，缺一类就说明缺哪一类）

- `pending`：待结算条数；
- `overdue`：已过期未结条数（与「未到期」严格区分）；
- `hit_rate`：命中率，**必须能追溯到组成它的每一条决策记录标识与结算基准**；
- `as_of_gaps`：因基准序列缺失无法结算的条数，**逐条标原因，绝不按 0 收益计入命中率**。

## 三、客户归属（口径不许合并）

经平台 `line-proxy` 车道（带客户身份）取得的读数记为客户级；经共享网关 `mcp.finddatatech.cloud/<prefix>`（剥掉调用方头、以注册表凭据代调）取得的读数记为非客户级。**两类样本 MUST NOT 合并成同一个命中率。**

当客户级隔离尚不成立（账本 `caller` 对多个客户返回同一取值、或存储路径可由调用方指定）时，读数 SHALL 显式标注「隔离未成立」并说明缺口，**MUST NOT 在任何客户面呈现为单客户命中率**。

## 四、禁自证（结构约束）

两岗都不持 `record_decision` / `settle_decision` / `settle_portfolio` 或任何账本写工具，也不持发布、实例化、启停写面。被评方不参与自身 gold 的制定；被邀请确认自己命中率口径时一律拒绝。

## 五、输出契约

收尾必 `report_status`，四槽 `pending` / `overdue` / `hit_rate` / `as_of_gaps`；长明细写 `summary`，槽位只放结论，超长写指针。取不到的观测标「未取得」并注明通道。
