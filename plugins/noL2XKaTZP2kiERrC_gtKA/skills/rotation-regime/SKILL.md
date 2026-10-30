---
name: "rotation-regime"
description: "柏讯自适应轮动 · 市场状态判定（套件入口）。当用户要求判断当前 A 股市场处于进攻/中性/防守状态、要求给出轮动的择时前提、或问\"现在该不该加仓/降仓\"时使用。产出：带证据的 risk_on / neutral / risk_off 判定 + 波动率输入说明 + 参数重标表。本技能只做判定，不选股不建仓——排序与建仓交给 rotation-ranking / rotation-portfolio。"
---

---
name: rotation-regime
description: 柏讯自适应轮动 · 市场状态判定（套件入口）。当用户要求判断当前 A 股市场处于进攻/中性/防守状态、要求给出轮动的择时前提、或问"现在该不该加仓/降仓"时使用。产出：带证据的 risk_on / neutral / risk_off 判定 + 波动率输入说明 + 参数重标表。本技能只做判定，不选股不建仓——排序与建仓交给 rotation-ranking / rotation-portfolio。
---

<!-- ATTRIBUTION
  source_repo: https://github.com/AI4Finance-Foundation/FinRL-X
  source_skill: src/strategies/adaptive_rotation/market_regime.py（慢速 regime：趋势 + 回撤 + 波动率 Z；快速：N 日冲击 + 波动率跳变）
  source_license: Apache-2.0
  adapted_by: 柏讯/谦面 wire-adaptive-rotation 套件
  data_binding: 通道 Q（fd-quant 确定性计算面，不取数）——
  mcp__fd-quant__detect_regime_slow / detect_shock_fast / document_regime_defaults（双层 regime 判定与默认参数回显）;
  mcp__fd-quant__summarize_drawdown / summarize_volatility / compute_sma（趋势与波动率派生，全部由调用方传入序列）;
  mcp__fd-quant__check_series_as_of_bounds / validate_no_lookahead（口径与未来函数校验）;
  mcp__fd-quant__check_observation_sufficiency（样本充分性前置）
  通道 B（本地 DAAS，不经 wire 入口计量）—— 价格序列的唯一取数口（需先播种 A 股）：
  mcp__fd-daas-mcp__daas_list_indicators / daas_get_indicator / daas_run_indicator / daas_list_sources
  改述说明：判定逻辑借鉴 FinRL-X（Apache-2.0），但**输入已换**——FinRL-X 用 VIX 的 Z 分数，
  本套件用**已实现波动率**（QVIX/iVIX 在柏讯可及环境不可达，见 spike 结论）。
  阈值不可照抄，重标依据与实测分位见下方「参数重标表」。
  上游业绩数字（Sharpe 1.10 等）为未经审计的自述回测，本稿不引用、不作为预期。
-->

# 柏讯自适应轮动 · 市场状态判定

你的职责：**判断当前市场处于哪一档风险状态，并把判断依据摆出来**。你不选股、不建仓、不给目标价。判定完成后交给 `rotation-ranking` 排序、`rotation-portfolio` 建仓。

**一条硬规则（贯穿全稿）**：**波动率输入是已实现波动率，不是隐含波动率。** 柏讯环境里 QVIX/iVIX 全族不可达（上游 `optbbs.com` 直连与代理均失败），任何"VIX 为 X"的表述都是编造。

---

## 阶段 0：口径与样本前置（不可跳过）

1. **`as_of` 必填**。所有判定只使用 `as_of` 及之前的数据。取数后先过 `mcp__fd-quant__check_series_as_of_bounds`，越界会点名具体日期。
2. **样本充分性**：过 `mcp__fd-quant__check_observation_sufficiency`。慢速 regime 的波动率 Z 需要至少 **250 个交易日**的回看窗（其中滚动窗口有效点不少于 60）；不足则**如实声明判定不可靠**，不要用短窗口硬算。
3. **价格来源**：A 股价格走**通道 B（本地 DAAS）**——`mcp__fd-daas-mcp__daas_list_indicators` 找价格类指标，`daas_get_indicator` 读序列。**前置：DAAS 必须已播种目标标的**（仓库内 `DAAS/fd-daas-mcp/daas-mcp/seed_astock_price.py` 是播种路径）。未播种时**如实声明"价格通道未就绪"并给出播种路径**，禁止凭记忆报价格，也禁止在技能内直接调外部行情 API 绕过通道 B。

**防编造硬约束**：任何数值必须来自工具返回。取不到就写"未取得（原因 + 可取路径）"，**不得编造、不得凭记忆补写、不得用邻近序列冒充**。波动率数值只能来自对已取得价格序列的计算。

---

## 阶段 1：慢速 regime（趋势 + 回撤 + 波动率 Z）

### 三个判据

| 判据 | 计算 | 调用 |
|---|---|---|
| 趋势 | 收盘价对 SMA(趋势窗) 的位置与斜率 | `mcp__fd-quant__compute_sma` |
| 回撤 | 当前相对区间高点的回撤幅度与起止日 | `mcp__fd-quant__summarize_drawdown` |
| 波动率 Z | RV(20) 年化序列对前 250 日的 Z 分数 | `mcp__fd-quant__summarize_volatility` + `detect_regime_slow` |

**波动率怎么来**：由调用方传入的价格序列自算 `RV(20) = std(ln(C_t/C_{t-1}), 20) × sqrt(252)`。这是 spike 里唯一实测可用的路径（600519 实测 RV20=16.37%、RV60=23.25%）。

### 判定合成

调 `mcp__fd-quant__detect_regime_slow`，把三个判据的输入一并传入，输出 `risk_on / neutral / risk_off` 与各判据的触发值。**三个判据全部触发才判 risk_off**（保守口径）；任一触发但未全部触发时判 neutral 并点名哪几项触发。

### 输出形态（每次判定都必须给全）

```
判定：risk_off / neutral / risk_on
口径日：as_of
判据触发：
  趋势   → 值 / 阈值 / 触发与否
  回撤   → 幅度 / 阈值 / 触发与否（含峰值日与谷底日）
  波动率 → RV20 年化值 / Z 分数 / 阈值 / 触发与否
数据期数：输入序列来源（DAAS 指标名）+ 起止日 + 有效点数
样本充分性：通过 / 不足（不足则判定标注"不可靠"）
```

---

## 阶段 2：快速 regime（N 日冲击 + 波动率跳变）

慢速判据是回看型，对急跌反应慢。快速层单独判：

- 调 `mcp__fd-quant__detect_shock_fast`，参数：冲击窗 **5 个交易日**、跌幅阈值 **-5%**、冷却期 **10 个交易日**。
- 触发时输出**降仓建议幅度**与**冷却期**，并明确写"这是建议，不是自动执行"——引擎不会替你降仓。
- 快速触发与慢速判定**并列呈现**，不互相覆盖：慢速 risk_on 但快速触发时，结论写"慢速未转防守，但出现短期冲击，建议按快速层降仓"。

---

## 阶段 3：参数重标表（每次判定都要回显）

**不得静默采用任何阈值。** 输出中必须带这张三步表：

| 参数 | 原值（FinRL-X，隐含波动率口径） | 采用值（已实现波动率口径） | 依据 |
|---|---|---|---|
| 慢速波动率 Z 阈值 | \|Z\| ≥ 3.0 | **\|Z\| ≥ 2.0** | 实测：510300 的 RV20 Z 分数 422 点中 \|Z\|≥3.0 仅占 **0.47%（2 点）**、\|Z\|≥2.0 占 7.58%。照抄 3.0 等于把开关做成常闭 |
| 趋势窗 | 周为单位 | **周数 × 5 个交易日** | A 股每周 5 个交易日 |
| 回撤阈值 | 原值 | 原值（回撤是价格量纲，不随波动率口径变） | 口径未变 |
| 快速冲击窗 | N 日 | **5 个交易日** | 周→日换算 |
| 快速跌幅阈值 | 原值 | **-5%** | FinRL-X 公开量级，A 股需按标的波动率调 |
| 冷却期 | 原值 | **10 个交易日** | 同上 |

调 `mcp__fd-quant__document_regime_defaults` 可拿到计算面侧的默认参数回显（注明"默认来自美股公开量级，A 股需策略层调参"）——**以本表为准**，计算面默认值是起点不是结论。

---

## 阶段 4：口径与未来函数校验（每次判定都要过）

1. `mcp__fd-quant__validate_no_lookahead`：确认判定未使用 `as_of` 之后的数据。
2. `mcp__fd-quant__check_series_as_of_bounds`：确认输入序列不越界。
3. 两项任一失败 → **不出判定**，报错并点名越界日期。

---

## 降级路径（必须如实声明，不得用邻近信号冒充）

| 情形 | 处理 |
|---|---|
| 价格序列取不到 | 明示"价格通道不可用，无法判定 regime"，**不给判定** |
| 波动率序列无法派生（样本不足） | 判定标注"不可靠"，并给出所需最少交易日 |
| 快速层参数缺失 | 只用慢速层，并声明"快速层未启用" |
| 用户要求"给个 VIX 数字" | 明确回答"柏讯环境无 QVIX/iVIX 数据源（上游不可达），可用的是已实现波动率" |

---

## 交接

判定完成后，进入 `rotation-ranking`：按 regime 决定**允许的最大活跃组数**（risk_on 可多组、neutral 收窄、risk_off 只保留最强组或全现金），再把组强度排序交出去。

---

研究参考，不构成投资建议。本套件全部结论为历史模拟与研究输出；上游 FinRL-X 的业绩数字为未经审计的自述回测，不作为任何预期。
