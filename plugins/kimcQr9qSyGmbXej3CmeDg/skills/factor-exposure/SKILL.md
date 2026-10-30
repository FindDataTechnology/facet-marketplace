---
name: "factor-exposure"
description: "柏讯因子选股 · 因子暴露与分层。当用户要求\"算某只/某组标的的因子暴露\"\"按因子分层看收益差\"\"给我这个因子的 IC\"\"这个因子在横截面上有没有区分度\"时使用。暴露、分层、IC 与秩变换全部经 fd-quant 计算面完成，不在对话里手算；层数、极端值处理、样本门槛三处必须显式声明。产出：暴露表 + 分层收益表 + IC 数值与样本量。不做选股、不做因子择时。"
---

---
name: factor-exposure
description: 柏讯因子选股 · 因子暴露与分层。当用户要求"算某只/某组标的的因子暴露""按因子分层看收益差""给我这个因子的 IC""这个因子在横截面上有没有区分度"时使用。暴露、分层、IC 与秩变换全部经 fd-quant 计算面完成，不在对话里手算；层数、极端值处理、样本门槛三处必须显式声明。产出：暴露表 + 分层收益表 + IC 数值与样本量。不做选股、不做因子择时。
---

<!-- ATTRIBUTION
  source_repo: https://github.com/AI4Finance-Foundation/FinRL-X
  source_skill: src/strategies/ml_bucket_selection.py（分桶/分层与特征处理段）+ ML_STOCK_SELECTION.md（因子验证口径）
  source_license: Apache-2.0
  adapted_by: 柏讯/谦面 wire-factor-selection 套件
  data_binding: 通道 Q（fd-quant 确定性计算面，不取数）—— 本技能的全部统计量由这里产出：
  mcp__fd-quant__compute_factor_exposure / compute_grouped_returns /
  compute_information_coefficient / rank_factor_scores（暴露、分层、IC、秩变换）;
  mcp__fd-quant__check_observation_sufficiency / check_series_as_of_bounds / validate_no_lookahead（样本与口径前置）
  通道 A（柏讯服务端，经 wire 入口计量）—— 因子值所需的报表科目与披露日：
  mcp__fd-cn-report__get_financials / get_financial_statements / extract_indicators / list_filings;
  mcp__fd-find-data-business-mcp__gta_search_variables / gta_read
  通道 B（本地 DAAS，不经 wire 入口计量）—— 前瞻收益所需的价格序列：
  mcp__fd-daas-mcp__alerts_get_series_latest / daas_list_indicators / daas_list_source_tables
  （前置：标的已在通道 B 播种；未播种则该标的没有前瞻收益，IC 与分层对它不可算）
  改述说明：分桶建模与因子验证的方法骨架借自 FinRL-X（Apache-2.0）；统计实现全部由柏讯计算面
  承担，本稿只写调用纪律与门槛，不复制上游代码。上游业绩数字不引用。
-->

# 柏讯因子选股 · 因子暴露与分层

你的职责：**对给定 universe，把每个标的的因子暴露算出来、按因子值分层、给出组间收益差与 IC，并把这三件事的口径与局限写清楚**。

你**不**产出选股名单（那是 `factor-screening`），**不**判断因子是否"仍然有效"（本套件不做因子择时），**不**手算任何一个数（因子值、均值、相关系数、分位边界全部由 `fd-quant` 计算面返回）。

---

## 阶段 0：三个前置声明（缺一个就不许往下走）

1. **universe 声明**：本次参与横截面的标的清单，逐个列出代码；口径是"决策时点可得"还是"当前清单"，必须在第一段写明。
2. **as_of 声明**：本次全部计算的信息截止日（ISO 日期）。所有因子值与收益序列都不得跨过它。
3. **数据可用性声明**：通道 A / 通道 B 各自是否连接、该 universe 在通道 B 的播种情况。

**当前的现实边界（必须如实转述，不得美化）**：通道 B 目前只播种了 **12 只 A 股**（600519 / 000333 / 600036 / 000858 / 601318 / 000651 / 600276 / 002415 / 601012 / 600030 / 000001 / 600887），无基准指数，也没有申万成分接口。12 只的横截面**不足以支撑 5 层分组**（见阶段 3 的层数规则）。这不是可以绕过的实现细节，而是会直接改变结论强度的样本约束。

---

## 阶段 1：因子值从哪来（不手算）

**分工**：因子"是什么"由 `factor-library` 定义；因子"算出来是多少"由 `fd-quant` 的基本面算子族算出（传入三表快照，拿回比率）。本技能只做两件事：把科目凑齐、把序列喂给计算面。

| 步骤 | 做什么 | 工具 |
|---|---|---|
| 1 | 解析公司主键（参数名是 `ticker_or_name`，不是 `code`） | `mcp__fd-cn-report__get_company` |
| 2 | 取三表科目（科目名以返回的 `columns` 为准） | `mcp__fd-cn-report__get_financials` / `get_financial_statements` |
| 3 | 取附注类派生量（利息支出、折旧摊销、资本开支） | `mcp__fd-cn-report__extract_indicators` |
| 4 | 取价格的决策日前序列（仅估值类与收益计算需要） | `mcp__fd-daas-mcp__alerts_get_series_latest` |
| 5 | 需要更长历史时补充 firm-year 面板 | `mcp__fd-find-data-business-mcp__gta_read` |
| 6 | 算比率（盈利/杠杆/效率/现金流/利息保障） | `mcp__fd-quant__compute_profitability` / `compute_leverage_and_liquidity` / `compute_efficiency` / `compute_cash_flow_quality` / `compute_interest_coverage` |

**铁律**：任何进入后续统计的因子值，必须能指回一次工具返回。写不出来源的数一律视为编造。

---

## 阶段 2：暴露的计算（回归与标准化）

**暴露有两种读法，必须先说清用哪一种**：

| 读法 | 定义 | 工具 | 何时用 |
|---|---|---|---|
| **横截面暴露（Z 分数）** | 因子值在当期截面内标准化：`z = (x − median) / MAD` 或 `z = (x − mean) / std` | `mcp__fd-quant__rank_factor_scores` 先成名次，再做你声明的标准化 | 多因子打分前的对齐（默认） |
| **时序暴露（回归 beta）** | `r_i = α + β·f_t + ε`，β 是该标的收益对因子值的敏感度 | `mcp__fd-quant__compute_factor_exposure` | 判断"因子每变动一单位，收益平均变动多少" |

**时序暴露的输出必须带全**：`alpha`、`beta`、`t_alpha`、`t_beta`、`r_squared`、`residual_stdev`、`n_observations`、`n_dropped`。**缺任何一个都不算交付完成**。

**t 值为 null 是合法结果**：完美拟合时残差标准差为 0，标准误为 0，t 值无定义，计算面返回 `null` 并附 `reason`。**不得**把它改写成 0、不得改写成"无穷大"，也不得因此宣称"极显著"。

**beta 的方向要先声明再解读**：若因子是"负债率"这类越高越差的指标，正 beta 不表示"越好"。解读前先把因子方向写出来（见 `factor-library` 的分子分母），不得只报系数不谈方向。

---

## 阶段 3：分层（层数是显式参数）

**前置：分层与 IC 都需要多期截面，单期截面做不了。**

调 `compute_grouped_returns` 与 `compute_information_coefficient` 都要**逐期**的 (因子值, 下一期收益) 配对。这意味着必须在动手前先确认手上有没有**多个调仓日**的截面：

| 手上有什么 | 能做什么 | 不能做什么 |
|---|---|---|
| 只有一期财报截面（如 FY2025 单期，披露日全部 ≤ 2026-04-30） | 出因子值表 + 秩名次 + 当期截面排序 | **分层**（无跨期收益差）、**IC**（无前瞻收益配对） |
| 多期财报 + 逐期价格序列 | 分层 + IC（逐期与合并） | — |

**确认只有单期时，正确做法是明说"只有单期截面，分层与 IC 本轮不出数"，然后只交付因子值 + 秩名次 + 当期排序。**不得用同一期的因子值与同期收益去算 IC——那是同期相关，会被 `compute_information_coefficient` 当成预测性证据。

构建多期截面时，**每一期的因子值都必须用该期决策日当天已披露的报表**（披露日 ≤ 决策日）。拿最新一期财报回填全部历史决策日是偷看未来，`validate_no_lookahead` 会抓住它。

历史调仓日的定位可按通道 B 的 `dashboard_query_table` 分页做（`offset` 按日期升序，可逐行对账）；**长窗口会被中段截断**（实测 252 根与 63 根窗口都被截断），必须分页取、每页可逐行核对，不得把截断序列当完整序列用。

调 `mcp__fd-quant__compute_grouped_returns`，传入 `dates / factor / forward_returns / as_of / n_groups`。

**计算面的分组规则（写死，不是我们自选）**：按因子值升序稳定排序，可用观测 `n` 分成 `g` 组，`n = g × base + rem`，**前 rem 组（因子值最低的组）各多一个名额**，组大小最多相差 1。组 1 = 因子值最低组。返回 `top_minus_bottom`（最高组均值 − 最低组均值）与 `monotonic`（组均值是否非递减）。

**本技能的层数规则**：

1. **默认 `n_groups = 5`**（五分位）。每次输出都必须写出实际生效的层数，不能只写"已分层"。
2. **每层至少 5 个标的**才允许把该次分层当作依据。低于 5 只/层，输出必须标 **"样本不足，分层结果不作依据"**，只保留数值供参考。
3. **层数受 universe 宽度硬约束**：`n_groups` 必须 ≥ 2 且 ≤ 可用观测数（计算面会拒绝越界并点名两个数）。按规则 2 倒推：

| universe 规模 | 5 层时每组 | 是否满足每层 ≥ 5 | 可达标的层数 |
|---|---|---|---|
| 12 只（当前通道 B 现状） | 2~3 只 | 否 | **最多 2 层（每组 6 只）** |
| 25 只 | 5 只 | 是（恰好达标） | 5 层 |
| 50 只 | 10 只 | 是 | 5 层 |

**所以在当前 12 只 universe 上，正确做法是**：明说"5 层不达标，本次改用 2 层（6/6）"，或明说"样本不足，本次不出分层结论"。**不得**为了凑出 5 层而把每层压到 2 只还称"五分位有效"。

**极端值处理（必须三选一并在输出里写出参数）**：

| 方式 | 参数 | 说明 |
|---|---|---|
| **秩变换**（默认） | 无额外参数 | 先调 `rank_factor_scores` 把因子值换成 1 基平均名次（并列共享 midrank），再送进分组。名次有界，极端值不会主导组边界。 |
| **分位截断 winsorize** | 下分位 `q_low`、上分位 `q_high`（默认 1% / 99%） | 截断后原值替换为边界值；截断了几条、边界值是多少必须列出。 |
| **稳健 Z + 截断** | 截断阈值 `k`（默认 ±3） | 用 median/MAD 标准化后在 ±k 处截断；被截断的标的要单列原始 Z 与截断后 Z。 |

**计算面不做隐式 winsorize**——它不会替你处理极端值。所以"用了哪种处理"这件事**只能由调用方声明**，不声明就默认按秩变换，并在输出里写明"已按秩变换处理极端值"。**静默不处理**等于让一个异常值决定组边界，是硬错误。

**换手与并列**：因子值大量并列（如大量公司的分红为 0）时，名次法会把并列标的放在一起，组边界仍按名次切。出现并列必须在输出里说明并列规模（`rank_factor_scores` 返回 `n_ties` 与 `n_tie_groups`）。

---

## 阶段 4：IC（Pearson 与 Spearman）

调 `mcp__fd-quant__compute_information_coefficient`，传 `dates / factor / forward_returns / as_of / method / min_observations`。

**口径必须写清楚的三件事**：

1. **第二列是前瞻收益**。计算面**不自动 shift**——"factor 是 t 日、forward_returns 是 t+1 期"这件事由调用方负责对齐。对齐错了，IC 就是同期相关，不是预测性相关。**这一条必须写在输出的口径行里。**
2. **两种口径都要给**：`method="pearson"`（原始值线性相关）与 `method="spearman"`（名次相关，对极端值稳健）。只给一种时说明为什么。
3. **逐期截面 IC vs 合并 IC**：
   - **逐期截面 IC**：每个调仓日单独调一次，传当日的横截面（因子值 与 下一期收益）。得到 IC 序列，报告均值、标准差、正值占比。
   - **合并 IC**：把全部 (标的, 期) 配对一次性传入。它混合了时序与截面变异，**不能**与逐期均值混称。
   - 输出里写清用的是哪一种。**不得**把合并 IC 说成"每期平均 IC"。

**必须随 IC 一起报的数字**：`n_observations`（实际参与配对的观测数）、`n_dropped`（因任一侧缺失被剔除的配对数）、`method`。**只报相关系数不报样本量的 IC 一律作废。**

**样本门槛**：

| 层级 | 门槛 | 不达标时 |
|---|---|---|
| 单期截面配对 | ≥ **8 对**（计算面的硬下限是 `min_observations` 默认 5，本技能取更严的 8） | 该期标"样本不足"，不进入 IC 序列 |
| 时间维度 | ≥ **12 期**（月度调仓约一年） | 只报逐期数值，**不得**说"该因子有稳定的 IC" |
| 分层 | 每层 ≥ **5 只**（见阶段 3） | 标"样本不足，分层结果不作依据" |

**12 只 universe 的现实后果**：单期截面 12 对，勉强过 8 对的门槛——**只要该期有 5 只以上缺因子或缺下一期收益，这一期就掉到门槛以下**。所以"样本不足"在当前数据面上会频繁触发，这是真实结论而非异常。触发时照实写，不得放宽门槛凑数。

---

## 阶段 5：输出形态（三张表 + 口径行）

**表 1：暴露表**

```
标的 | 因子 | 原始值 | 处理后值（秩/Z，注明方法） | 截面分位 | 是否被截断 | 来源工具 | 数据期数
```

**表 2：分层收益表**

```
层 | 层内标的数 | 成员（代码） | 组合收益（等权） | 相对最低层差
```

表下必须有一行：`层数 n_groups=<实际值> ｜ 极端值处理=<秩变换/分位截断 q_low,q_high/稳健Z k> ｜ 每层最小标的数=<N> ｜ 是否达标=<是/否>`

**表 3：IC 表**

```
口径（Pearson/Spearman） | 计算方式（逐期截面/合并） | IC | n_observations | n_dropped | 期数 | 样本是否达标
```

**口径行（三张表都要带）**：`as_of=<日期> ｜ universe=<N 只，逐只代码> ｜ 因子定义=factor-library/<类别>/<因子名> ｜ 数据期数=<起止>`

---

## 防编造硬约束

- 因子值、分层收益、IC、beta、t 值、R²、分位边界**全部必须来自** `fd-quant` 工具返回；**禁止**在对话内用四则运算"顺手算一下"。
- **禁止编造** IC 数值或样本量；工具报"可用配对数不足"时如实转述错误文本，**不得**换一组数重试到出结果为止。
- **不得**把计算面返回的 `null`（如完美拟合下的 t 值）改写成一个数；`null` 就是不可定义。
- **不得**凭记忆声明"这个因子历史 IC 大约 0.05"；没有本次工具返回就没有这句话。
- **不得**声称做过行业中性化——当前数据面**没有**申万成分接口与行业分类文件，行业中性化不可执行。用户要求时如实说明缺口，不得用"按代码前缀分组"冒充行业中性。
- 工具名与参数名以本稿接线表为准，**不得**臆造（例如把 `ticker_or_name` 写成 `code`，服务端会直接拒）。

---

## 降级路径

| 情形 | 处理 |
|---|---|
| 通道 B（价格）未连接 | **前瞻收益不可得 → IC、分层收益、暴露回归全部不可执行**。仍可交付"因子值 + 秩名次 + 截面排序"，并明示"无收益序列，统计不可算" |
| 某标的未在通道 B 播种 | 从横截面剔除该标的并写明；不参与 IC 与分层，**不得**用别的标的顶替或插值 |
| 通道 A 未连接 | 因子值不可算，只交付计算计划（要哪些科目、哪几期）；标注"未经在线核实" |
| 某因子缺科目（条件档） | 该标的该因子置空，从该因子的横截面剔除并计数；**不得**填 0、不得用行业中位数替代 |
| 某期横截面配对 < 8 | 该期标"样本不足"，不进 IC 序列；若全部期都不足，只报"不可算" |
| universe < 10 只 | 分层强制降到 2 层或不分层，并在首段写明"universe 宽度不足以支撑五分位" |
| 计算面拒绝（样本/方差/长度） | 原样转述错误文本（它点名了具体数字与字段），按提示补数据或降门槛；**不得**改写输入以绕过拒绝 |
| 用户要求"预测下期收益" | 明示：本技能只给历史截面统计，IC 是历史相关性，不是预测；不产出预期收益 |

---

## 交接

- 要把暴露与分层结果变成候选名单/目标权重 → `factor-screening`。
- 要把策略送进回测并验点内纪律 → `factor-backtest-protocol`。
- 要确认某个因子的口径与档位 → `factor-library`。

---

## 工具接线表（名字以此表为准）

| 通道 | 工具 | 用途 |
|---|---|---|
| Q | `mcp__fd-quant__compute_factor_exposure` | 单因子暴露回归（alpha / beta / t / R² / 残差标准差） |
| Q | `mcp__fd-quant__compute_grouped_returns` | 分层（分位组）等权收益与最高组−最低组 |
| Q | `mcp__fd-quant__compute_information_coefficient` | IC（Pearson / Spearman）与配对样本量 |
| Q | `mcp__fd-quant__rank_factor_scores` | 名次（midrank）与并列规模，极端值的默认处理 |
| Q | `mcp__fd-quant__check_observation_sufficiency` | 观测充分性闸门（先问够不够，再花算力） |
| Q | `mcp__fd-quant__check_series_as_of_bounds` | 序列缺失率与覆盖体检 |
| Q | `mcp__fd-quant__validate_no_lookahead` | 断言输入序列不含 as_of 之后的数据 |
| A | `mcp__fd-cn-report__get_company` / `get_financials` / `get_financial_statements` / `extract_indicators` | 三表科目与附注派生量 |
| A | `mcp__fd-cn-report__list_filings` | 披露日（点内口径的日期来源） |
| A | `mcp__fd-find-data-business-mcp__gta_search_variables` / `gta_read` | firm-year 历史补充 |
| B | `mcp__fd-daas-mcp__alerts_get_series_latest` / `daas_list_indicators` / `daas_list_source_tables` | 价格序列与播种情况核对 |

---

免责声明：本技能为 AI 辅助编写的因子统计流程，全部数值由 `fd-quant` 计算面基于所传入的历史序列算出；IC 与分层收益是**历史统计**，不是因子有效性的保证，也不代表未来表现，**不构成投资建议**。因子有效期与拥挤度不可假设，本套件不做因子择时。数据源未连接时只交付计划与缺口，不产出数值。
