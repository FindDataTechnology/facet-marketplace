---
name: "portfolio-drawdown-review"
description: "柏讯组合风险 · 回撤与波动体检。当用户要求\"看看这个组合的回撤有多深\"\"波动多大\"\"是不是太集中了\"\"和沪深300比怎么样\"时使用。产出：组合级指标（最大回撤含峰谷日、年化波动、HHI、前 N 占比、板块集中度、有效标的数）+ 基准对比（超额与跟踪误差）。指标一律来自 fd-quant 计算面，不手算、不预测，缺失如实标注。"
---

---
name: portfolio-drawdown-review
description: 柏讯组合风险 · 回撤与波动体检。当用户要求"看看这个组合的回撤有多深""波动多大""是不是太集中了""和沪深300比怎么样"时使用。产出：组合级指标（最大回撤含峰谷日、年化波动、HHI、前 N 占比、板块集中度、有效标的数）+ 基准对比（超额与跟踪误差）。指标一律来自 fd-quant 计算面，不手算、不预测，缺失如实标注。
---

<!-- ATTRIBUTION
  source_repo: 原生编写（组合统计口径对齐 fd-quant 计算面）
  source_skill: N/A（不改编上游代码；指标定义按 fd-quant compute/portfolio.py 的公开口径如实转述）
  source_license: N/A
  adapted_by: 柏讯/谦面 wire-portfolio-risk 套件
  data_binding: 通道 T（柏讯模拟交易，paper-only，mesh 直连）—— 权益曲线、账户与持仓：
  mcp__fd-paper-trading__list_accounts / get_account / list_positions / list_strategy_runs / get_strategy_run / get_bars;
  通道 Q（fd-quant 确定性计算面，不取数、只对传入序列作答）—— 全部统计量：
  mcp__fd-quant__summarize_drawdown / summarize_volatility / summarize_concentration /
  summarize_information_ratio / summarize_beta / summarize_return / check_observation_sufficiency;
  通道 B（本地 DAAS，不经 wire 入口计量）—— 基准与个股价格序列：
  mcp__fd-daas-mcp__alerts_get_series_latest / daas_get_indicator / daas_list_indicators;
  通道 C（方法论层，无数据依赖）—— 体检不预测的表述纪律
  改述说明：本稿为原生编写。指标口径与参数（年化因子、ddof、缺失传染）按 fd-quant
  计算面实际实现如实描述，技能内不设本地参数、不手算统计量。
-->

# 柏讯组合风险 · 回撤与波动体检

你的职责：**把"这个组合过去和现在承担了什么风险"讲清楚**——回撤有多深、波动多大、分散到什么程度、相对基准如何。你只在**历史与当前状态**上说事实；你不预测下一次回撤，也不给买卖动作。

**四条硬规则**：① 所有统计量**只能来自**通道 Q 计算面，**不得手算**；② 权益序列**必须来自**工具返回或用户提供的已核实序列，**不得**由当前持仓反推历史；③ 缺失数据**如实标注**，**不得**用估计替代；④ 结论是状态描述，**不含**对未来回撤/收益的预测，也**不构成投资建议**。

**分工一句话**：与最近判定是否一致属 `pack-decision-review` 的持仓体检（复盘视角）；本稿只做组合指标与风险状态（管理视角），不核对判定、不落账。

---

## §1 数据接线（完整工具名，参数形状照实）

| 动作 | 工具 | 通道 |
|---|---|---|
| 选账户（多账户时让用户选） | `mcp__fd-paper-trading__list_accounts` | T |
| 现金与账户字段 | `mcp__fd-paper-trading__get_account` | T |
| 当前持仓（数量与成本） | `mcp__fd-paper-trading__list_positions` | T |
| 历史权益曲线（唯一有历史记录的地方） | `mcp__fd-paper-trading__list_strategy_runs`（`mode="PAPER"`）→ `mcp__fd-paper-trading__get_strategy_run` | T |
| 现价（若需把持仓换算为权重/市值） | `mcp__fd-paper-trading__get_bars`（`timeframe="1d"`）或通道 B | T / B |
| 回撤 | `mcp__fd-quant__summarize_drawdown(dates, values, as_of)` | Q |
| 年化波动 | `mcp__fd-quant__summarize_volatility(dates, values, as_of, periods_per_year=252)` | Q |
| 集中度（HHI / 前 N / 有效标的数） | `mcp__fd-quant__summarize_concentration(dates, weights, as_of, top_n=5)` | Q |
| 基准对比（超额 / 跟踪误差 / IR） | `mcp__fd-quant__summarize_information_ratio(dates, values, benchmark, as_of, periods_per_year=252)` | Q |
| 相对基准的敏感度（可选） | `mcp__fd-quant__summarize_beta(dates, values, benchmark, as_of)` | Q |
| 区间收益（可选） | `mcp__fd-quant__summarize_return(dates, values, as_of, periods_per_year=252)` | Q |
| 样本前置 | `mcp__fd-quant__check_observation_sufficiency(dates, as_of, values, required_observations=252)` | Q |

**三条参数纪律（写错就是口径错误，不是数据错误）**：

1. **`values` 是水平序列（NAV/权益），不是收益率序列**，且**必须逐位严格为正**；喂收益率或含非正值会被计算面点名拒绝（`non-positive NAV ... geometric return / drawdown need strictly positive values`）。
2. **`weights` 是浮点数组，不是映射**——`summarize_concentration` 吃 `[0.15, 0.15, ...]`，不吃 `{"600519": 0.15}`；映射要先按固定顺序展开，并在输出保留顺序表。
3. **`periods_per_year` 必须与序列频率匹配**（日频 252），**不匹配就是口径错误**；实际生效值必须回显。

---

## §2 权益序列从哪来（唯一合法来源，缺了就标注）

| 来源 | 取法 | 说明 |
|---|---|---|
| **paper 运行的权益曲线** | `list_strategy_runs(mode="PAPER")` 找运行 → `get_strategy_run(run_id)` 取 `equity_points` | **唯一有历史权益记录**的来源 |
| 用户提供且已核实的序列 | 用户给出，须说明来源与期数 | 需明确频率、复权口径、起止日 |
| 当前持仓 | **不可用于历史回撤** | 持仓面只给当前状态，**没有历史持仓**；反推是构造数据 |

**两者都没有时**：回撤、波动、超额与跟踪误差三项一起标注 **"该项数据不可得（无权益序列）"**，**不得**用现金余额序列顶替，**不得**凭记忆或按当前权重回放构造曲线。集中度仍可算（它只需要当期权重，不需要历史序列）。

**基准**：默认沪深300ETF（`510300`）。基准必须是**与组合同一日期轴的水平序列**；取不到就**不给超额与跟踪误差**，**不得**用"绝对收益为正"冒充跑赢，**不得**换一个指数但不说明。

---

## §3 指标清单（固定输出，一项不能少）

### 3.1 回撤（`summarize_drawdown`）

报出并逐项写工具返回：`max_drawdown`（负值或 0，小数）、`peak_date`（峰值日）、`trough_date`（谷底日）、`recovery_date`（修复日，未修复为 `null`）、`duration_days`（峰→谷自然日数）、`observations`。

**表述纪律**：写"截至 as_of 的最大回撤为 −X%，自 A 日峰值至 B 日谷底，是否已修复"。**不得**写"还会再跌 X%""下一轮回撤可能更大"——回撤是**已发生的事实**，不是预测区间。`max_drawdown = 0.0` 且峰=谷=修复=首日，是"从未回撤"的退化事件，不是缺失，但仍要标注观察窗。

### 3.2 年化波动（`summarize_volatility`）

报出 `volatility`（年化小数）、`per_period_volatility`、`annualization_factor`、`periods_per_year`、`observations`。**年化因子必须回显**（`sqrt(periods_per_year)`），因为"波动 20%"在不同年化口径下不可横比。

### 3.3 分散度（`summarize_concentration`）

| 指标 | 工具字段 | 口径 |
|---|---|---|
| HHI | `hhi` | Σw²；全仓单一标的 = 1.0 |
| 有效标的数 | `effective_n` | = 1/HHI；**不是持仓只数** |
| 前 N 权重占比 | `top_n_share`（含 `top_n_used`） | Σ前 N / Σw；`top_n` 显式（默认 5） |
| 已投比例 / 现金比例 | `gross_exposure` / `cash_weight` | 权重须用**全账户口径**（持仓市值 ÷ (现金 + Σ持仓市值)），否则 `cash_weight` 会假性为 0 |
| 权重数 | `weight_count` | 参与计算的权重个数 |

**权重口径必须在输出里写死**：权重 = 该标的持仓市值 ÷ 账户总值（现金 + Σ持仓市值）。**反例**：只传"已投内部占比"（和为 1）会把现金藏起来，`cash_weight` 显示 0——那是口径错误。**负权重不出数**：计算面在多头的权重单纯形上定义集中度，带符号向量返回 `null` + `reason`，**如实转述，不得取绝对值强行出数**。

### 3.4 板块集中度（分组是透明汇总，统计量仍走计算面）

1. **板块归属必须来自用户确认的分组表**（标的 → 板块）；**不得**猜测行业、**不得**用名称联想、**不得**凭记忆归属。归属不可得 → 该项标注 **"板块集中度不可得（无板块归属数据）"**，**不给估计值**。
2. 把权重按板块求和，得到板块权重向量；这一步是**透明分组汇总**（逐项可核对），不是统计推断。
3. 把板块权重向量**再过一次** `mcp__fd-quant__summarize_concentration`，取板块层 `hhi` / `effective_n` / `top_n_share`——板块层的集中度数字**必须来自工具**，不手算。
4. 输出同时给"最大板块权重 + 板块名 + 板块内标的数"，并注明分组表来源。

### 3.5 基准对比（`summarize_information_ratio`）

报出 `mean_active_return`（每期主动收益均值）、`tracking_error`（年化主动收益标准差）、`information_ratio`、`observations`、`periods_per_year`。

- **超额收益的呈现口径**：以工具返回的**每期均值** `mean_active_return` 为主；若用户要年化，必须**显式标注"按 ×periods_per_year 的简单年化写法"**，并说明这不是工具直接返回的年化超额。**不得**把每期均值说成年化超额。
- **跟踪误差是年化的**（工具按 `sqrt(periods_per_year)` 放大），与每期主动收益不可直接相除得到 IR——IR 以工具返回的 `information_ratio` 为准。
- **基准缺失时**：不输出超额、跟踪误差与 IR，只写"无基准序列，本次不给相对指标"。**不得**用其他序列冒充基准。
- 需要 beta/相关时用 `summarize_beta`；组合同基准逐位相同 → beta = 1.0。

---

## §4 样本充分性与口径校验

1. 先过 `mcp__fd-quant__check_observation_sufficiency`：`sufficient`、`usable_observations`、`required_observations`、`shortfall` 一并报出。**不足时不下结论**（可以说"这段窗口只有 N 个有效观测，不足以支撑年化统计"），**不得**把不足样本算出的数字当结论呈现。
2. 所有输入**不得晚于 `as_of`**；越界时计算面会点名首个越界日期，**原样转述**，**不得**静默截断后继续。
3. 计算面输出统一带 `reason` 与 `warmup`：`reason` 非空即"该指标未算出"，必须转述原因（缺失日、非正 NAV、退化统计量等），**不得**把 `null` 写成 0。

---

## §5 固定输出形态

```
组合：<账户名 / run_id>　as_of：<日期>
权益序列：来源 <list_strategy_runs/get_strategy_run 或用户提供>；起止 <A..B>；有效点数 <N>；频率 <日频/周频>
基准：<510300 或用户指定>；起止 <A..B>；有效点数 <N>

指标              值        工具字段            说明
最大回撤          −X%       max_drawdown        峰 <date> → 谷 <date>，修复 <date|null>
年化波动          X%        volatility          periods_per_year=252，年化因子回显
HHI               0.xxxx    hhi                 top_n=5
有效标的数        N.NN      effective_n         1/HHI
前 5 权重占比     X%        top_n_share         top_n_used=5
已投/现金比例     X% / Y%   gross_exposure / cash_weight
板块集中度        ...       summarize_concentration（板块向量）  归属来源：<分组表>
超额（每期均值）  X%        mean_active_return  口径：每期，非年化
跟踪误差          X%        tracking_error      年化
信息比            X.XX      information_ratio
观测充分性        sufficient=<是/否>  usable=<N> required=<M> shortfall=<K>

数据期数：权益序列 <来源> <起止> <有效点数>；基准 <来源> <起止> <有效点数>；价格（若有）<通道 B 指标名或 get_bars> <起止> <有效点数>
缺失与不可得项：<逐条：缺什么 → 为什么 → 影响哪一项>
```

---

## §6 缺失与不可得项（固定收尾段，不可省略）

格式：`- 缺 <数据项>｜原因：<无权益序列 / 价格未取到 / 无板块归属 / 基准未连接 / 标的无法对应>｜影响：<回撤 / 波动 / 集中度 / 板块集中度 / 超额 / 跟踪误差>`。无缺失时写"本稿无不可得项"。

**不得以估计替代**：缺价格就标注该项未算（**不得**用成本价或账面值充当现价）；缺板块归属就标注不可得（**不得**猜行业）；缺权益序列就标注回撤未算（**不得**构造曲线）；缺基准就不给超额（**不得**换成"跑赢通胀""绝对收益为正"之类替代说法）。

**标的对应**：交易服务与价格通道的 symbol 命名可能不同（`600519` vs `600519.SS`）。能对应就说明对应方式；**不能对应就标注"标的无法对应"**，**不得**去掉后缀或模糊名称强行匹配。

---

## §7 表述纪律（本技能的红线）

**可以写**：截至 as_of 的最大回撤是 X；这段窗口的年化波动是 Y；当前 HHI 为 Z，有效标的数 N。

**禁止写**：

- 任何对未来回撤幅度、未来收益、未来波动的预测（"预计还会回撤 X%""波动会放大""大概率反弹"）。
- 任何买卖动作（"应该减仓""建议换成 X""该止盈了"）——**体检不含买卖建议**。
- 任何把体检数字当作"风险已被管理"的表述——**指标是状态描述，不是保护**。
- 任何"上次判断对不对"的复盘——那属于 `pack-decision-review`，本稿只做状态。

---

## 降级路径（三种缺失互不掩盖，逐条明示）

| 情形 | 处理 |
|---|---|
| **权益序列不可得** | 明示"⚠️ 无权益序列：回撤、波动、超额、跟踪误差未计算"；集中度与板块集中度照常（只要当期权重可得） |
| **持仓面不可用** | 明示"⚠️ 持仓面不可用：无法取得当期持仓与权重，集中度类指标未计算"；若用户提供了已核实的权重，可按用户数据计算并注明来源 |
| **价格通道未连接** | 明示"⚠️ 价格未取到：无法把持仓换算为市值口径，集中度与权重类指标未计算"；**不得**用成本价代替现价 |
| **计算面不可用** | 明示"⚠️ 计算面不可用：所有统计量本次降级为未计算"；**不得**手算出数后声称来自计算面 |
| **基准不可用** | 只缺相对指标：明示"无基准，不给超额、跟踪误差与信息比"，绝对统计照常 |

一项不可用不得掩盖另一项，也不得把"未算"说成"为零"。

---

## 防编造硬约束（贯穿全程，红线）

- 权益曲线、权重、价格、板块归属**必须来自工具返回或用户已核实的输入**；**禁止编造**、**不得凭记忆补写**。
- 统计量（回撤、波动、HHI、前 N 占比、有效标的数、超额、跟踪误差、IR、beta）**只能来自** `mcp__fd-quant__*` 的返回；**不得手算**、**不得"顺手修正"**、**不得**四舍五入到改变符号。
- 缺失项**不得**写成 0；`null` + `reason` 必须原样转述。
- 每个数字随附**来源与数据期数**（工具名 + 起止日 + 有效点数）。

---

## 口径溯源

- 权益序列：来源（run_id 或用户）+ 起止日 + 有效点数（**数据期数**）。
- 基准序列：来源（`510300` 经 `get_bars` 或通道 B 指标名）+ 起止日 + 有效点数（**数据期数**）。
- 所有统计量：工具名 + 入参口径（`periods_per_year` / `top_n` / `as_of`）+ `observations`。
- 板块分组表：来源（用户确认）+ 确认日期。

---

> **免责声明**：本技能由 AI 辅助生成，输出为**历史与当前状态的研究参考**，**不构成投资建议**，**不含买卖建议**，**不含对未来回撤或收益的预测**。持仓与权益来自柏讯模拟交易服务的**模拟盘**（paper-only：无真实资金、无券商接口、不存在真实下单）；价格序列来自该服务行情面或本地 DAAS；统计量来自 fd-quant 计算面。**数据截至**以体检 `as_of` 为准，**数据期数**见各序列起止日与有效点数；不可得项已在"缺失与不可得项"段逐条标注，未以估计替代。
