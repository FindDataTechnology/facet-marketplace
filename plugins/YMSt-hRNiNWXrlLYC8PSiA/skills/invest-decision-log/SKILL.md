---
name: "invest-decision-log"
description: "每轮产出判定即落账；到期项在复盘窗口结算并写反思。含基准纪律与「本轮无判定」写法。"
---

---
name: invest-decision-log
description: 晨会岗位的落账与到期结算纪律（投研晨会套件的记账闸）。当岗位本轮产出了判定（Rating）、或需要查回自己历史判定与教训、或复盘窗口要结到期项时使用。产出即落账，不攒批、不事后补记；本轮确实没有判定时必须写明"本轮无判定"及原因；修订＝新记录 + revises 指针，历史判定永不被就地改写；到期结算由本稿的列举入口驱动，价格与基准序列一律由调用方传入（账本不取数）。
---

<!-- ATTRIBUTION
  source_repo: 原生编写
  source_skill: 原生（结构对齐本仓 decision-review-log 的落账纪律与 decision-review-settlement 的结算纪律，
    但把"每轮必落"作为本稿的硬约束——那两个稿子面向复盘套件的通用用法，不约束晨会节奏）
  source_license: 不适用（原生文本）
  data_binding: 通道 Q（fd-quant 决策账本面，不取数、不联网、不调 LLM）——
    mcp__fd-quant__record_decision / mcp__fd-quant__list_pending_decisions /
    mcp__fd-quant__settle_decision / mcp__fd-quant__retrieve_decisions /
    mcp__fd-quant__summarize_pack_performance；
    结算窗价格与基准序列由调用方从通道 B（mcp__fd-daas-mcp__*）取，本稿不取数。
  改述说明：原生编写。与 decision-review-log 的分工——那个管"怎么记才对"，本稿管"每轮必须记"。
-->

# 柏讯 · 晨会落账（每轮必落 · 到期必结）

## 口径声明（每次输出都要带）

本次取数的三样东西必须原样转述，不得凭记忆补：**来源**（`source_id` 与库内 `source_used`）、
**复权口径与锚点**（`qfq`/`hfq`/`none`/`not_applicable` + 锚点）、**数据期数**（一律取工具返回的
`as_of_max`，不得写死日期）。

价格与市值走通道 B；账本只做存储与算术（通道 Q），**账本不取数**——序列由你传入。

---

## 四条硬规则（后面每段都是它的展开）

① **产出即落账**：本轮产出了判定就调 `record_decision`，不得攒批、不得等下一轮补记。
② **无判定也要留痕**：本轮确实没有可落账的判定（取数失败、覆盖不足、无观察标的）时，
在产出正文里写明 **"本轮无判定 + 原因"**；**不得以"没写"表达同一件事**——
账本为 0 既可能是"没判定"也可能是"没落账"，只有正文能分开这两者。
③ **幂等与追加**：幂等键是 `(caller, as_of, symbol, pack, skill)`；重复提交被拒并返回既有
`decision_id`。修订＝**新记录 + `revises` 指针**，原记录逐字不动。
④ **不美化**：落账内容必须与产出正文一致；判定有好有坏，账本不是成绩单。

---

## §1 落账（每轮必做）

| 动作 | 工具 | 必填 |
|---|---|---|
| 落一条判定 | `mcp__fd-quant__record_decision` | `as_of`、`pack`、`skill`、`payload`（Rating 完整实例）；单体记录 `record_kind='single'` 且 `symbol` 必填 |
| 查回自己的历史 | `mcp__fd-quant__retrieve_decisions` | 只回**结算日不晚于检索日**的记录（点内检索，防未来信息） |
| 按 pack 看战绩 | `mcp__fd-quant__summarize_pack_performance` | — |

- `pack` 一律写 **`投研晨会`**，`skill` 一律写 **`invest-decision-log`**——这两列是命中率的分母主语，
  留空会让"哪份方法更准"永远无法回答。
- `payload.basis` 写判定理由（1–3 句）；`evidence` 填引用数组（本轮取到的事实）。
- **没有判定时**：不落账，但在正文写 `本轮无判定：<原因>`（例：`本轮无判定：通道 B 价格水位
  早于 as_of 超阈值，未出评级`）。

## §2 到期结算（复盘窗口）

| 步骤 | 工具 | 要点 |
|---|---|---|
| ① 列待结算 | `mcp__fd-quant__list_pending_decisions` | 按调用者隔离；未到期的**留在清单里**并标 `matured=false` 与缺的交易日估计，**不是报"查不到"** |
| ② 取结算窗序列 | 通道 B（`mcp__fd-daas-mcp__*`） | 标的与基准各一条；**基准必须是指数序列** |
| ③ 结算 | `mcp__fd-quant__settle_decision` | 传 `benchmark_symbol` 显式声明；持有期默认 5 个交易日 |

**基准纪律（硬）**：指数基准 MUST 显式传 `benchmark_symbol='000300.SH'`（沪深300），
MUST NOT 用同码个股表——`000001` 在 A 股既是上证综指（`000001.SH`）也是平安银行（`000001.SZ`），
DAAS 里 `scraw_000001_daily` 装的是后者（2026-11-06 收盘 11.81，综指在 4000 上下）。
拿错了不会有任何报错：算术复核逐位一致、归因行还写着"上证综指"，**两条判据都不会红**。
判据取库内自述口径（`scraw_configs.columns_json.source` 须为 `index_zh_a_hist`），不从表名推断。

**结算失败要分清三种**：

- 持有期未满 ⇒ 账本**拒绝结算**、不落任何行、状态仍 `pending`（"半窗 alpha"比没有 alpha 更坏）；
- 价格水位过旧 ⇒ 记 `insufficient_data` 且 `gap_kinds` 点名「价格水位」，给两侧日期；
- 基准不可得 ⇒ 记 `insufficient_data` 且点名「基准序列」。
  **MUST NOT** 以旧价格模拟成交后记 `hit`/`miss`，MUST NOT 用个股序列冒充基准充数。

## §3 结算之后的反思

结算记录可带 `reflection`（2–4 句，由你生成后传入）。反思缺失**不阻塞**结算，但缺失会让
"同一份方法错在哪"永远学不到。反思不得改写历史判定，只能作为新字段追加。

---

## 反例（这些都不算"落账了"）

- 在正文里写"已结 N 条"而不调 `record_decision`——正文不是账本。
- 把三只票的判定合成一条记录——幂等键是单标的的，合成会让 `retrieve_decisions` 查不到其中两只。
- 改了 `payload` 再提交同一 `(caller, as_of, symbol, pack, skill)` 想"更正"——会被幂等键拒绝；
  更正的正路是**新记录 + `revises`**。
