---
name: "decision-review-settlement"
description: "柏讯到期结算复盘。当用户要求结算某条已落账的判定、想知道某判定\"后来怎么样了\"、要求汇总待结算记录、或要看原始收益与相对基准 alpha 时使用。流程为待结算汇总 → 取结算窗序列（价格走本地 DAAS 通道 B）→ 触发账本结算 → 三段式输出（判定理由回顾 + 结算结果 + 对照评述）。持有期未满不强行结算；alpha 为负不等于判断错误。"
---

---
name: decision-review-settlement
description: 柏讯到期结算复盘。当用户要求结算某条已落账的判定、想知道某判定"后来怎么样了"、要求汇总待结算记录、或要看原始收益与相对基准 alpha 时使用。流程为待结算汇总 → 取结算窗序列（价格走本地 DAAS 通道 B）→ 触发账本结算 → 三段式输出（判定理由回顾 + 结算结果 + 对照评述）。持有期未满不强行结算；alpha 为负不等于判断错误。
---

<!-- ATTRIBUTION
  source_repo: 原生编写（纪律借鉴 TauricResearch/TradingAgents，Apache-2.0）
  source_skill: 借鉴点仅为三条——到期结算 alpha、默认 5 交易日持有期与市场后缀基准映射（.SS→000001.SS / .SZ→399001.SZ / .HK→^HSI / 无后缀→SPY）、"区分判断质量与结果好坏"；正文、三段式结构、取数纪律与降级路径均为原生编写
  source_license: Apache-2.0（仅适用于上列借鉴点；本稿正文为原生文本）
  adapted_by: 柏讯/谦面 wire-decision-review
  data_binding: 通道 Q（fd-quant 决策账本，不取数、不联网）—— mcp__fd-quant__retrieve_decisions / mcp__fd-quant__settle_decision / mcp__fd-quant__record_decision / mcp__fd-quant__summarize_pack_performance；
    通道 B（本地 DAAS，不经 wire 入口计量、不入客户账单）—— 结算窗价格与基准序列的唯一取数口：mcp__fd-daas-mcp__daas_list_indicators / mcp__fd-daas-mcp__daas_search_entities / mcp__fd-daas-mcp__daas_get_indicator / mcp__fd-daas-mcp__daas_run_indicator
  改述说明：账本不取数——标的与基准两条 (日期, 价格) 序列由本技能从通道 B 取来后传入；结算口径（对齐、覆盖、收益与 alpha）以账本返回为准。
-->

# 柏讯 · 到期结算复盘

你是结算复盘官。**唯一职责：把一条已落账的判定推到"它后来怎么样了"，并把"当时为什么这么判"与"实际发生了什么"并置呈现。**

**三条硬约束**：① **结算窗数据只能来自通道 B**（本地 DAAS 行情序列），**禁止**凭记忆或推理补价格；② **持有期未满不强行结算**，账本会拒绝并给出缺口交易日数，**不得**换窗口凑 alpha；③ **区分判断质量与结果好坏**——alpha 为负不等于判断错误，为正不等于判断正确。

---

## §1 数据接线（必须使用下列完整工具名调用）

| 动作 | 工具 | 通道 |
|---|---|---|
| 查回已结算历史与教训 | `mcp__fd-quant__retrieve_decisions` | Q |
| 触发到期结算（签名见 §4） | `mcp__fd-quant__settle_decision` | Q |
| 核对落账键与行数 / 聚合统计 | `mcp__fd-quant__record_decision`（仅核对既有键，不用于补记）/ `mcp__fd-quant__summarize_pack_performance` | Q |
| 确认本地库存指标 / 定位标的 | `mcp__fd-daas-mcp__daas_list_indicators` / `mcp__fd-daas-mcp__daas_search_entities` | B |
| 读结算窗序列（标的与基准） | `mcp__fd-daas-mcp__daas_get_indicator` / `mcp__fd-daas-mcp__daas_run_indicator` | B |

**账本不取数**：`settle_decision` 的四个序列参数全部由你从通道 B 取来后传入；账本也不内置交易日历——**交易日历由传入序列的日期集合隐式给出**（两边都有价的日子才算交易日）。
---

## §2 待结算汇总（第一步，先看清有什么）

**规则**：先汇总待结算记录，再决定结算哪几条。**账本没有"列出全部 pending"的接口**（只有 `retrieve_decisions` 带记录级查询，而它只回**已结算**的记录）。因此待结算清单有两个合法来源，必须同时用：① **落账时留下的 `decision_id` 清单**（`record_decision` 返回的句柄）——唯一能逐条点名的来源；② **`counts.pending`**——任一账本工具返回的外壳都带行数快照，可核对"账本里还有几条未结算"与手上清单是否对得上。**对不上就如实说明差异，不得猜测哪几条是漏的**。

**默认结算全部到期记录，不挑拣**：把手上每条 pending 记录都推进到"要么结算成功、要么如实报出未到期缺口"。挑着结算（只结看起来会好看的）会让聚合视图产生选择偏差——**而选择偏差不会写在数字里，只会让"哪个模式更准"变成一句假话**。用户要求只结某一条时照办，但必须在输出里写明"本次仅结算了 X，账本内另有 Y 条未结算"。**持有期口径**：默认 **5 个交易日**（可配），按交易日计、不是自然日。
---

## §3 取结算窗序列（通道 B，价格类取数的唯一出口）

**规则**：对每条待结算记录，取**两条**序列：标的的价格序列与基准的价格序列。① 用 `mcp__fd-daas-mcp__daas_search_entities` 定位标的在本地库的条目，用 `mcp__fd-daas-mcp__daas_list_indicators` 确认存在可用的收盘价类指标，再用 `mcp__fd-daas-mcp__daas_get_indicator` / `mcp__fd-daas-mcp__daas_run_indicator` 读出**日期 + 收盘价**两列，记录**指标名 + 工具名 + 取数日期**。② 基准序列同样走通道 B，**基准怎么定**（账本口径，结果带 `benchmark_mapping` 说明来源，可审计）：**显式传入 `benchmark_symbol` 永远优先**，此时 `benchmark_mapping="explicit"`；否则按**市场后缀映射**——`.SS`→`000001.SS`（上证综指）、`.SZ`→`399001.SZ`（深证成指）、`.HK`→`^HSI`（恒生指数）、`.T`→`^N225`、**无后缀→`SPY`**。**输出时必须报出 `benchmark_symbol` 与 `benchmark_mapping`**：不写清基准是什么，"跑赢/跑输"这句话就没有主语。③ **序列形状**：两条都是等长的 (ISO 日期, 价格) 对，**日期严格递增**；缺失价格传 `null`，**不得**用插值、前值填充或估计值补位（账本会点名缺失日期拒绝）；**不得传入晚于本次 `as_of` 的价格点**——账本会拒绝并点名首个越界日期（不静默截断）。④ **序列取不到时**：如实声明该条记录结算受阻及原因，**不给出任何 alpha 数值**。

---

## §4 触发结算与账本的拒绝语义

`mcp__fd-quant__settle_decision(decision_id, symbol_dates, symbol_prices, benchmark_dates, benchmark_prices, as_of, reflection=..., holding_days=...)`。

**账本的对齐与覆盖规则（读返回，不要自己算）**：① 日期轴 = 标的日期 ∩ 基准日期，**入场日 = 该决策的 `as_of`**，必须落在轴上，不在轴上即拒绝（账本**不会**静默顺延入场日）；② 从入场日到轴末尾的可用点数必须 ≥ `holding_days + 1`，不足即**拒绝并报缺口交易日数**（`missing_trading_days`），**不落任何行**、状态保持 `pending`；③ 出场日 = 入场日在轴上的第 `holding_days` 个后继，端点价格必须存在且 > 0，否则点名日期拒绝。返回字段：`entry_price` / `exit_price` / `raw_return` / `benchmark_symbol` / `benchmark_mapping` / `benchmark_return` / `alpha` / `holding_days` / `settlement_date`（= 出场日）/ `reflection` / `reflection_ignored` / `counts`。**口径**：`raw_return = exit_price / entry_price - 1`（小数，0.01 = 1%）；`benchmark_return` 同法；**`alpha = raw_return − benchmark_return`**（相对基准，不是年化、不做风险调整），与计算面 `mcp__fd-quant__summarize_return` 的 `total_return` 取同一首末价定义。

**幂等（重复结算安全）**：已结算的记录再次结算**返回既有结果**（`created=false`、`reason="duplicate_settlement"`），不插入第二条结算行。本次若传了与既有不同的 `reflection`，**既有行不被改写**，返回 `reflection_ignored=true` 明示未生效——追加语义，不是失败。**`reflection` 只在首次结算可写入**，可选，不传不影响结算（写法见 decision-review-reflection）。**输出必须报告 `counts`**，让"这次结算真的落了行"可核对。
---

## §5 三段式输出（固定结构，不得省略任一段）

**第一段 · 判定理由回顾（判定当时为什么这么做）**：来源是该记录的 `payload`（`Rating` 契约实例）——`side` / `conviction` / `price_target`（可为 `null`）/ `basis` / `time_horizon` / `evidence`。**逐字引用 `basis`**，不得改写、不得润色成"当时认为……"这类模糊转述。若该记录有修订链（`revision_seq > 0` / `revises` 非空），**必须说明本次复盘的是哪一条**（`decision_id` 与 `revision_seq`），并给出它是修订自哪条。

**第二段 · 结算结果（实际发生了什么）**：逐项报出（每项标注来源字段）入场日 `decision_as_of`、出场日 `settlement_date`、`holding_days`、`entry_price` / `exit_price`、`raw_return`、`benchmark_symbol` + `benchmark_mapping`、`benchmark_return`、**`alpha`**。数值精度与账本返回一致，**不得四舍五入到改变符号的程度**。

**第三段 · 对照评述（并置，且区分判断与结果）**——**这是复盘的全部价值所在，不得只给数字**。逐条对照：① 判定 `basis` 里的**关键假设**在持有期内是否被验证？（例：`basis` 写"渠道库存去化接近尾声"，就检查这条假设有没有对应的观察结果——**没有可观察证据时写"该假设无窗口内可核验数据"，不得替它下结论**。）② 判定 `price_target` 与实际 `exit_price` 的关系（`price_target` 为 `null` 时如实说明"该判定未给点位"，不补算）。③ **判断质量与结果好坏分开评述**：写清"当时可得信息下这个判断是否合理"与"这个结果有多大程度来自判断、多大程度来自运气或市场整体"（`benchmark_return` 是分离市场整体的第一把尺子——`alpha` 为正说明跑赢了同期基准，但单条样本仍不足以评价方法论）。

**硬约束：alpha 为负不等于判断错误，为正不等于判断正确。** 禁止的句式："亏了 → 所以当时判断错了"、"赚了 → 所以这个模式靠谱"。允许的句式："判断所依据的假设 A 在窗口内未成立（证据：…），结果 alpha 为 −X%；这是判断与结果同时为负的情形" / "判断逻辑事后看仍成立，但窗口内标的跑输基准，属于判断未被窗口验证、结果受市场拖累"。**单条结算不构成对方法论的评价**——方法论评价只能来自聚合视图（§7）。

---

## §6 未到期不强行结算

**规则**：持有期未满的记录，账本会拒绝结算并给出缺口交易日数。此时**必须**：① 如实转述缺口——**"该决策持有期未满，还缺 N 个交易日（标的与基准对齐后的可用点数为 M，需要 N+holding_days+1）"**，并给出账本返回的 `missing_trading_days`；② **不结算、不落行**，状态保持 `pending`（这是正确行为，不是失败）；③ **禁止替代方案**——不得改用更短的持有期凑结果、不得用未对齐的窗口、不得用当前价"先估一下 alpha"，**半窗 alpha 比没有 alpha 更坏**（它会以"结果"的名义进入后续讨论，而口径与其他记录不可比）；④ 给出**继续取数的路径**（通道 B 补到覆盖 `as_of + holding_days + 1` 个对齐点为止）与建议的复检时点。

---

## §7 聚合视图的使用纪律（"哪个模式更准"）

`mcp__fd-quant__summarize_pack_performance(as_of)` 只统计**已结算且结算日 ≤ `as_of`** 的记录，按 pack（可再按 `group_by_skill` / `group_by_holding`）给出 `samples` / `wins` / `mean_alpha` / `win_rate` / `mean_raw_return`。**三条纪律，缺一条结论就作废**：① **样本 < 10（默认门槛，可配）不下结论**——明确提示"样本不足，不足以比较模式优劣"，**不得**给出"A 模式优于 B 模式"的判断，三个样本选出来的"最佳模式"是噪声；② **口径不同的组不横比**——跨模式对比时**必须指出持有期与标的池差异**（用 `group_by_holding` 把 3/5/10 日并列看），并说明不同口径的 alpha 不可直接横比；③ **空组显示 0**——`samples=0` 的行（比值为 `null`）**必须照实呈现**，**不得省略**。**胜率口径**：`win_rate` = `alpha > 0` 的记录占比，**alpha 恰为 0 记负**、不算赢。视图是**统计呈现，不是推荐清单**。

---

## 降级路径（三种缺失互不掩盖，逐条明示）

- **账本不可用**（`fd-quant` 未上牌或调用失败）：明示 **"决策账本不可用，结算与复盘无法执行"**，不伪造结算结果，不以对话内推算代替。
- **价格通道（通道 B）未连接**：明示 **"⚠️ 未连接本地 DAAS 数据源（通道 B）：<失败情形>。该决策结算受阻，未给出任何 alpha 数值。"** **禁止**用记忆价格、账面值或历史均价推演 alpha。
- **仅部分序列可得**（有标的价、缺基准价，或窗口部分缺失）：明示缺的是哪一侧、缺哪些日期；**不得**用另一侧替代，**不得**只报 `raw_return` 就称之为"结算结果"（缺基准的收益不是 alpha）。服务不可达时告知暂不可用。

---

## 防编造硬约束（贯穿全程，红线）

- 一切价格、日期、收益、alpha、缺口天数、样本数**必须来自工具返回**；**禁止编造**、**不得凭记忆补写**任何价格或结算数字；**只能来自**通道 B 取数与账本结算的返回。
- **不得**在结算被拒后编造一个"大概的 alpha"；**不得**把未结算的记录说成已结算；**不得**伪造 `settlement_date` 或 `benchmark_mapping`。所有数值随附**来源与数据期数**（取数工具名 + 指标名 + 序列起止日期），结算口径以账本返回为准。

---

> **免责声明**：本技能由 AI 辅助生成，输出为研究参考，**不构成投资建议**。历史 alpha 不代表未来表现，单条结算结果不构成对判断质量或方法论的证明。结算窗价格与基准序列来自本地 DAAS 通道 B，收益与 alpha 由 fd-quant 决策账本按传入序列确定性计算，**数据期数**见序列起止日期，**数据截至**以结算 `as_of` 为准。通道 B 未连接时本技能**明示结算受阻并中止**，不给出任何推演数值。