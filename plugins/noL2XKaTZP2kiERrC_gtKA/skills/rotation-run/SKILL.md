---
name: "rotation-run"
description: "柏讯自适应轮动 · 闭环运行与解读。当用户要\"把这份权重跑一下\"\"回测结果怎么看\"\"模拟盘为什么没成交/权重为什么偏了\"时使用。产出：权重模式回测结果（含成本与基准）+ 权重快照解读 + 偏离归因。终产物是可执行权重与运行记录，不是一段买入建议。"
---

---
name: rotation-run
description: 柏讯自适应轮动 · 闭环运行与解读。当用户要"把这份权重跑一下""回测结果怎么看""模拟盘为什么没成交/权重为什么偏了"时使用。产出：权重模式回测结果（含成本与基准）+ 权重快照解读 + 偏离归因。终产物是可执行权重与运行记录，不是一段买入建议。
---

<!-- ATTRIBUTION
  source_repo: https://github.com/AI4Finance-Foundation/FinRL-X
  source_skill: src/strategies/adaptive_rotation/walk_forward.py（走前验证 + 无未来函数断言）
  source_license: Apache-2.0
  adapted_by: 柏讯/谦面 wire-adaptive-rotation 套件
  data_binding: 通道 T（柏讯模拟交易，paper-only，mesh 直连）——
  mcp__fd-paper-trading__create_strategy / update_strategy / get_strategy（spec 落地与版本）;
  mcp__fd-paper-trading__run_backtest / get_strategy_run / list_strategy_runs（回测与运行记录）;
  mcp__fd-paper-trading__start_strategy / stop_strategy（模拟盘启停）;
  mcp__fd-paper-trading__list_positions / list_orders / get_account（快照与偏离）;
  mcp__fd-paper-trading__get_bars / search_instruments（数据覆盖）
  通道 Q（fd-quant 确定性计算面）——
  mcp__fd-quant__split_walk_forward_windows / validate_no_lookahead（走前窗口与无未来函数）;
  mcp__fd-quant__summarize_return / summarize_volatility / summarize_drawdown / summarize_sharpe /
  summarize_sortino / summarize_calmar / summarize_turnover / summarize_beta（统计，全部由传入序列算出）
  改述说明：闭环形态借鉴 FinRL-X 的 walk_forward 模块（Apache-2.0）。统计一律由通道 Q 产出，
  不自行计算。上游业绩数字不引用。
-->

# 柏讯自适应轮动 · 闭环运行与解读

你的职责：**把 `rotation-portfolio` 产出的权重变成一次真实回测与一段可解释的运行记录**。终产物是**可执行权重 + 运行记录 + 偏离归因**，不是"建议买入 XX"。

---

## 阶段 0：终产物形态纪律（先说清）

本套件的交付物是：

1. **`TargetWeights` 契约实例**（JSON，权重向量）
2. **weight 模式策略 spec**（可被 fd-paper-trading 创建与运行）
3. **回测结果与权重快照**（带成本、带基准、带统计）

**不是**：一段"我认为应该配白酒和电子"的文字。若用户只要文字结论，说明本套件的产出形态，并给出上述三件中的至少前两件。

**防编造硬约束**：回测数字、持仓、订单、成交价全部必须来自工具返回。模拟盘未启动或快照不可得时，如实写"快照不可得（原因）"，**不得伪造持仓或成交记录**；基准不可得时不给超额指标，**不得用绝对收益冒充跑赢**。

---

## 阶段 1：spec 落地（先过数据覆盖门禁）

1. `mcp__fd-paper-trading__search_instruments` 逐个确认 universe 标的存在。
2. `mcp__fd-paper-trading__get_bars` 确认回测窗口内有真实数据。**任一标的缺数据，回测会被数据覆盖度门禁拒绝**（错误会点名缺失区间）——在创建前就查，不要等失败。
3. `create_strategy`（或 `update_strategy` 改版本）落 spec。创建成功后拿回 strategy id 与版本号。

**注意**：universe 规模受组配置约束（`max_universe_size`，默认 20）；顺序有意义（第一个是主标的，决定"是否有新 bar"）。

---

## 阶段 2：回测（走前 + 成本 + 基准）

1. **走前窗口**：调 `mcp__fd-quant__split_walk_forward_windows` 生成窗口列表（默认训练 60 / 测试 20 / 步长 20 个交易日，可配）。测试窗严格晚于训练窗。
2. **无未来函数**：每个窗口过 `mcp__fd-quant__validate_no_lookahead`。任一窗口失败 → **不出回测结论**。
3. `mcp__fd-paper-trading__run_backtest` 跑回测，**必须带成本**（A 股默认：佣金 0.025% 双边、印花税 0.05% 卖出、滑点 5bp 双边）。
4. **统计全部向通道 Q 要**，不自行计算：
   - `summarize_return`（年化收益）、`summarize_volatility`（年化波动）
   - `summarize_sharpe` / `summarize_sortino` / `summarize_calmar`
   - `summarize_drawdown`（含峰值日/谷底日）
   - `summarize_turnover`（换手）、`summarize_beta`（对基准）
5. **基准必须给**：默认沪深300ETF（510300）。**没有基准就不给超额指标**——不得用"绝对收益为正"冒充跑赢。

**输出回测表**：

```
窗口 | 起止 | 收益 | 波动 | Sharpe | Sortino | 最大回撤（起止） | 换手 | 对基准超额
合计/均值 | ...
成本假设：佣金 X / 印花税 Y / 滑点 Z
数据期数：行情来源（通道 B 指标名）+ 回测窗口起止 + 有效 bar 数 + 基准序列起止
```

---

## 阶段 3：模拟盘运行

1. `start_strategy` 启动（paper-only，无真实资金）。
2. 运行中每有新 bar，按 `every_n_bars` 调仓。**引擎不会自动止损**——见 `rotation-portfolio` 的归属声明；若用户要止损，必须已在权重序列里表达。
3. `stop_strategy` 停止。

---

## 阶段 4：权重快照与偏离解读（本技能的核心价值）

每次调仓后调 `list_positions` / `get_account` / `list_orders`，产出一张快照表：

```
标的 | 目标权重 | 实际权重 | 偏离 | 归因
```

**偏离的三类正常原因（必须先排除，再谈异常）**：

| 归因 | 判据 | 是否异常 |
|---|---|---|
| **取整残差** | 偏离 < 1e-6，且权重和 < 1 的部分在现金里 | 正常 |
| **价格漂移** | 两次调仓之间标的涨跌导致实际权重偏移 | 正常（下次调仓修正） |
| **未成交/部分成交** | `list_orders` 有未成交或部分成交记录 | 需说明原因（停牌/涨跌停/流动性） |
| **真实偏离** | 以上都不成立，且偏离 > 阈值（默认 2%） | **异常，必须点名并给修复路径** |

**缩量有因**：若某标的实际权重显著低于目标，先查成交量与涨跌停状态，再下结论。**不得**把"没买到"说成"策略主动减仓"。

---

## 阶段 5：参数重标可见性（每次运行都要给）

回测与运行结果里，**实际生效的阈值必须回显**：

```
regime 慢速 Z 阈值：|Z| ≥ 2.0（原值 3.0，因口径由隐含波动率改为已实现波动率而重标）
快速冲击：5 日 / -5% / 冷却 10 日
调仓频率：every_n_bars = 20
单组上限 40% / 单票上限 15%
```

---

## 阶段 6：迭代挂点（对接复盘套件）

本套件的一次运行不是终点。闭环的下一环是**真实结算与归因**：

1. 把本次产出的判定（`TargetWeights` 或 `Rating`）经 `pack-decision-review` 的 `decision-review-log` **落账**；
2. 持有期到后由 `decision-review-settlement` **按真实价格结算 alpha**；
3. 用结算出的 alpha 反推阈值是否需要再调（例如：止损过紧导致频繁被扫出 → 放宽移动止损；regime 阈值过松导致频繁切换 → 提高 Z 阈值）。

**这条路径必须写进输出**，让用户知道参数不是一次标定就固定，而是按实盘 alpha 迭代。挂点指向 `pack-decision-review` 套件。

---

## 降级路径

| 情形 | 处理 |
|---|---|
| 标的缺数据 | 明示缺失区间与标的，**不出回测** |
| 走前窗口不足 | 明示所需最少 bar 数，不给"全样本一次回测"冒充走前 |
| 基准序列不可用 | 明示"无基准，不给超额指标"，只给绝对统计 |
| 模拟盘未启动 | 说明快照不可得，**不伪造持仓** |
| 用户问"止损会自动执行吗" | 明确回答"不会"，并给出两条替代路径（写进权重序列 / 人工执行） |

---

## 交付清单（一次完整运行应产出）

1. 回测表（含成本假设与基准）
2. 走前窗口列表与无未来函数校验记录
3. 权重快照表（含偏离归因）
4. 参数重标回显
5. 迭代挂点说明（指向复盘套件）

---

研究参考，不构成投资建议。历史回测与模拟盘结果不代表未来表现；上游 FinRL-X 的业绩数字为未经审计的自述回测，不作为预期。
