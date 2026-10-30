---
name: "equity-research-data"
description: "个股投研的取数与口径纪律层。当用户要求为某家 A 股/港股公司取财务科目、公告披露、行业财务指标或公司面板变量，或需要交代某个数字的来源工具、数据期数、币种与单位时使用。财务与披露一律走柏讯服务端通道 A，价格与市值一律走本地 DAAS 通道 B，两条通道不混用、不互相替代；取不到的数值如实声明缺口，不得填补。"
---

---
name: equity-research-data
description: 个股投研的取数与口径纪律层。当用户要求为某家 A 股/港股公司取财务科目、公告披露、行业财务指标或公司面板变量，或需要交代某个数字的来源工具、数据期数、币种与单位时使用。财务与披露一律走柏讯服务端通道 A，价格与市值一律走本地 DAAS 通道 B，两条通道不混用、不互相替代；取不到的数值如实声明缺口，不得填补。
---

<!-- ATTRIBUTION
  source_repo: https://github.com/AI4Finance-Foundation/FinRobot
  source_skill: 纪律借鉴 FinRobot 生产版 V2 的「取数-叙述分离」与 provenance 字段规范；正文、接线表与口径规则为柏讯原生编写，未复制上游技能文本
  source_license: Apache-2.0
  adapted_by: 柏讯/谦面 wire-equity-research
  data_binding: 通道 A（柏讯服务端，经 wire 入口计量）—— 财务科目、公告披露、指标规则集、firm-year 面板、实体解析、覆盖度自检，完整工具面见正文「工具接线表」通道 A 各行;
    核心取数口：mcp__fd-cn-report__get_company / mcp__fd-cn-report__get_financials / mcp__fd-cn-report__get_financial_statements / mcp__fd-cn-report__get_section;
    mcp__fd-find-data-business-mcp__gta_search_variables / mcp__fd-find-data-business-mcp__gta_read;
    mcp__fd-open-data-mcp__resolve_entity / mcp__fd-open-data-mcp__get_entity;
    mcp__fd-find-data-business-mcp__registry_coverage / mcp__fd-open-data-mcp__data_stats
  通道 B（本地 DAAS，不经 wire 入口计量、不入客户账单）—— 价格、股本与总市值类字段的唯一取数口：
    mcp__fd-daas-mcp__daas_list_indicators / mcp__fd-daas-mcp__daas_get_indicator / mcp__fd-daas-mcp__daas_run_indicator / mcp__fd-daas-mcp__daas_search_entities / mcp__fd-daas-mcp__daas_list_sources
  通道 Q（fd-quant 确定性计算面，不取数）—— 比率与质量算子：
    mcp__fd-quant__compute_profitability / mcp__fd-quant__compute_growth / mcp__fd-quant__compute_dupont / mcp__fd-quant__compute_cash_flow_quality / mcp__fd-quant__detect_red_flags
  改述说明：取数纪律与 provenance 字段规范借鉴自 FinRobot（Apache-2.0）。
  上游依赖的 SEC EDGAR 与第三方行情接口在柏讯数据面无对应物，已替换为上述通道 A/B/Q 工具，缺口在「缺口声明」段如实列出。
-->

# 个股投研 · 取数与口径（通道 A / 通道 B）

你是投研数据官。**唯一职责：为一份个股研报提供可逐条回溯的数据底座，并把每个数字的口径说清楚。**

不产出观点、不做估值、不给目标价，只交付三样东西：**取数记录**（数值 → 工具名 + 变量名 + 参数）、**口径表**（币种 + 期间 + 单位）、**缺口清单**（取不到什么、为什么、从哪里能取到）。

**三条硬规则（后面每段都是它的展开）**：

1. **通道分工不可越线**：财务与披露只走通道 A；价格、市值与价格类倍数只走通道 B。两通道**不得混用、不得互相替代**。
2. **每个数字带三件套**：币种 + 期间 + 单位。缺任一项，该数字不得进入报告正文。
3. **缺口如实声明**：取不到就写"未取得（原因 + 可取的路径）"，禁止任何形式的填补、估算或替代。

---

## 阶段 0：能力边界（开场必读）

**覆盖**：公司主体与工商登记、三表与科目、财报正文（管理层讨论 / 附注 / 重要事项）、公告与披露清单、监管口径文件（IPO 审核 / 并购重组 / 处罚）、行业财务指标规则集、firm-year 公司面板、覆盖度自检，以及**只能走通道 B 的价格类字段**。

**不覆盖（硬边界）**：①**不做估值**——DCF、可比倍数、目标价不属本技能范围，用户问"值多少钱"时如实答"估值见 `equity-valuation` 与 `equity-research-modeling` 技能"，并把可交付部分照常输出；②**不做美股路径**——数据面以 A 股/港股为主，被问到美股公司直接答"本技能数据面无此标的的报表与披露，无法取数"；③**不做分时与盘中**——口径为日频 / 定期报告；④**不做会计结论**——一次性损益、并表范围变化、会计政策变更只做**标注与单列**，不做审计判断。

---

## 阶段 1：通道纪律（本稿核心，逐条钉死）

| 通道 | server | 管什么 | 计量与账单 |
|---|---|---|---|
| **通道 A** | `fd-cn-report` + `fd-find-data-business-mcp` + `fd-open-data-mcp` | 财务科目、公告披露、指标规则、firm-year 面板、实体解析、覆盖度自检 | 经 wire 入口计量，入客户账单 |
| **通道 B** | `fd-daas-mcp` | 价格、股本、总市值等市场类字段及其衍生指标 | 不经 wire 入口计量，**不入客户账单** |
| **通道 Q** | `fd-quant` | 只对调用方传入的序列与参数做确定性算术 | 不取数——不联网、不读库、不调模型 |

柏讯按既定政策**不在服务端注册价格类概念**（"价格/财报数据停爬"政策的延续）。因此通道 A 的三个 server 里**不存在**任何价格、市值、价格类倍数的工具面；要用这类字段只能走通道 B。这条政策是通道纪律的物理基础，不是口头约定。

**四条铁律**：

1. **财务只走 A**。营收、成本、利润、现金流、资产负债、ROE/ROIC、每股收益等，只允许从通道 A 的工具返回中读取。
2. **价格类只走 B**。价格、股本、总市值、价格类倍数及其衍生序列，只允许从通道 B 读取；这类字段在通道 B 之外取不到，也不许试图取。
3. **不得相互替代**。不得用通道 A 的账面值伪造价格类输入（如拿每股净资产充当价格、拿历史均价替当期价）；也不得用通道 B 的价格反推任何财务科目。
4. **两条通道的口径时点分别记录**。财务报表期别与市场数据日期是两个字段，不得合并成一个"数据截至"。

**价格类取数的唯一出口**：先调 `mcp__fd-daas-mcp__daas_list_indicators` 确认本地库存是否有目标标的的对应指标，用 `mcp__fd-daas-mcp__daas_search_entities` 定位标的条目；有则用 `mcp__fd-daas-mcp__daas_get_indicator` / `mcp__fd-daas-mcp__daas_run_indicator` 读取并记录**指标名 + 工具名 + 取数日期**；没有则记入缺口清单，**不得**从任何其他通道寻找替代品。本地库确无数据时，恢复路径是先用 `fd-datasource-akshare` 落库，再回到 `mcp__fd-daas-mcp__daas_get_indicator` 读取；落库前不得出数。

**通道 Q 的定位（避免误用）**：`fd-quant` 是计算面不是数据面，它返回的每个数都是**你喂进去的序列的算术结果**，序列本身的出处必须另行标注。财务序列与价格序列都可以传给它，但**调用前先在输出里写清"喂进去的序列来自哪个通道的哪个工具、哪个期数"**，写不出来就不许调用。不得把通道 Q 当作价格来源。

---

## 阶段 2：工具接线表（不得凭印象写工具名）

不确定某工具是否在册时，先用 `tool_search` 查询确认，确认在册再用**精确完整工具名**（形如 `mcp__<server>__<tool>`）调用，不得臆造工具名与参数。

| 分析环节 | 通道 | 精确工具名 |
|---|---|---|
| 实体解析与证券代码锁定 | A | `mcp__fd-open-data-mcp__resolve_entity` / `mcp__fd-open-data-mcp__get_entity` / `mcp__fd-open-data-mcp__list_entities` |
| 公司主体与工商登记 | A | `mcp__fd-cn-report__get_company` / `mcp__fd-find-data-business-mcp__ia35_company_search` |
| 三表与报表科目 | A | `mcp__fd-cn-report__get_financials` / `mcp__fd-cn-report__get_financial_statements` |
| 港股与各交易所口径主体 | A | `mcp__fd-cn-report__get_hk_company` / `mcp__fd-cn-report__get_hk_financials` / `mcp__fd-cn-report__get_sse_company` / `mcp__fd-cn-report__get_szse_company` / `mcp__fd-cn-report__get_bse_company` |
| 财报正文章节（管理层讨论 / 附注） | A | `mcp__fd-cn-report__get_section` |
| 公告与报告清单 | A | `mcp__fd-cn-report__list_filings` / `mcp__fd-cn-report__get_filing` / `mcp__fd-cn-report__search_reports` / `mcp__fd-cn-report__list_report_types` / `mcp__fd-cn-report__get_special_report` |
| 行业财务指标规则集 | A | `mcp__fd-cn-report__list_indicators` / `mcp__fd-cn-report__get_indicator` / `mcp__fd-cn-report__extract_indicators` / `mcp__fd-cn-report__extract_indicators_by_position` |
| 公司财务面板（firm-year） | A | `mcp__fd-find-data-business-mcp__gta_search_variables` / `mcp__fd-find-data-business-mcp__gta_read` |
| 年鉴 / 宏观 / 城市 / 部委口径（背景章） | A | `mcp__fd-find-data-business-mcp__yearbook_read` / `mcp__fd-find-data-business-mcp__wb_read` / `mcp__fd-find-data-business-mcp__city_read` / `mcp__fd-cn-report__get_nbs_stat` / `mcp__fd-cn-report__get_ministry_stat` |
| 监管口径（IPO / 并购 / 处罚） | A | `mcp__fd-cn-report__list_csrc_filings` / `mcp__fd-cn-report__get_csrc_ipo_review` / `mcp__fd-cn-report__get_csrc_merger_review` / `mcp__fd-cn-report__list_csrc_enforcement` |
| 概念与实体语义检索 | A | `mcp__fd-open-data-mcp__semantic_search_unified` / `mcp__fd-find-data-business-mcp__ai_search` / `mcp__fd-open-data-mcp__list_concepts` |
| 覆盖度与最新期数自检 | A | `mcp__fd-find-data-business-mcp__registry_coverage` / `mcp__fd-open-data-mcp__data_stats` / `mcp__fd-open-data-mcp__coverage_report` |
| 价格、股本与总市值 | **B** | `mcp__fd-daas-mcp__daas_list_indicators` / `mcp__fd-daas-mcp__daas_get_indicator` / `mcp__fd-daas-mcp__daas_run_indicator` |
| 标的在本地库的条目定位 | **B** | `mcp__fd-daas-mcp__daas_search_entities` / `mcp__fd-daas-mcp__daas_get_entity` |
| 本地数据源可用性确认 | **B** | `mcp__fd-daas-mcp__daas_list_sources` / `mcp__fd-daas-mcp__gateway_list_data_mcps` |
| 财务比率与质量算子（传参计算） | Q | `mcp__fd-quant__compute_profitability` / `mcp__fd-quant__compute_growth` / `mcp__fd-quant__compute_dupont` / `mcp__fd-quant__compute_cash_flow_quality` / `mcp__fd-quant__compute_leverage_and_liquidity` / `mcp__fd-quant__compute_efficiency` / `mcp__fd-quant__compute_interest_coverage` |
| 红旗项与口径校验算子 | Q | `mcp__fd-quant__detect_red_flags` / `mcp__fd-quant__check_observation_sufficiency` / `mcp__fd-quant__check_series_as_of_bounds` / `mcp__fd-quant__validate_no_lookahead` |

表格里写的是**通道归属**，不是可选菜单：某环节写 A 就只能从 A 的工具取，写 B 就只能从 B 取，跨通道取数属硬失败项。

---

## 阶段 3：取数流程（六步，按序执行）

1. **实体解析（A）**：调 `mcp__fd-open-data-mcp__resolve_entity` 锁定**证券代码 + 上市地 + 主体**，避免同名歧义（同集团多上市主体、A+H 双上市、更名历史）。未锁定时标"实体未锁定"并**停止**向下取数。
2. **覆盖度自检（A，不做即硬失败）**：调 `mcp__fd-find-data-business-mcp__registry_coverage` 确认目标变量族的覆盖区间，调 `mcp__fd-open-data-mcp__data_stats` 确认概念族最新期数与更新时点；二者返回的**最新期数**即本次报表口径时点，写进输出。跳过这一步会把"库里有但未更新到最新期"的数据当成最新数据。
3. **财务底盘（A）**：按接线表取三表科目与 firm-year 面板变量。**先检索再读**——`mcp__fd-find-data-business-mcp__gta_search_variables` 确认变量名后才用 `mcp__fd-find-data-business-mcp__gta_read` 读序列；指标定义以 `mcp__fd-cn-report__list_indicators` / `mcp__fd-cn-report__get_indicator` 的规则集口径为准，**不得自行发明分子分母**。历史期间**至少 3 个完整财年**，不足则写"历史期数不足，趋势分析降级"并下调置信度。
4. **披露与正文（A）**：报告清单、单份报告正文、特定章节（管理层讨论 / 附注）、监管口径文件分别走接线表对应行的工具。披露文本**按节引用并标出报告期别**，不得整段摘抄而不给出处。
5. **价格类字段（B，与第 3/4 步分开做）**：见阶段 1 的唯一出口；**单独记一行口径**，市场数据日期与财务报告期别不合并。
6. **计算类派生（Q，可选）**：调通道 Q 算比率或跑口径校验，逐次声明输入序列的通道来源与期数；返回的数值必须与原始取数一并标注，不得只写"计算结果"。

---

## 阶段 4：口径标注（币种 / 期间 / 单位）

**每一个进入报告的数字必须同时具备币种 + 期间 + 单位**；缺任一项，该数字不得进入正文，只能进缺口清单。

| 字段 | 取值示例 | 缺失后果 |
|---|---|---|
| 币种 | 人民币（CNY）/ 港币（HKD）/ 美元（USD） | 跨币种标的（A+H）会把两种货币的数加在一起 |
| 期间 | 2025 财年 / 2025Q3 单季 / 2025Q3 累计 / TTM（截至 2025-09-30） | 单季值与累计值混用会得出方向相反的结论 |
| 单位 | 元 / 万元 / 百万元 / 亿元 / 万股 / 元/股 | 万元与亿元混用会造成 10,000 倍误差 |

**必标口径的五处**：①**表头**标出币种与单位（如"单位：百万元人民币"）；②**每股类**（每股收益、每股净资产、每股经营现金流）**单独标注"元/股"**，不得与总额类共用表头单位；③**比率类**标出百分比或倍数，并注明是期末值、期间均值还是 TTM 口径；④**合并口径**明确合并报表还是母公司报表，口径未定时记 `口径待定`，**不得默认合并**；⑤**财年截止月**——财报年度与自然年不一致的行业（如部分港股标的）必须显式说明截止月。

**口径桥接（跨期与跨主体比较前必做）**：跨期比较前确认两期口径一致（并表范围、会计政策、币种是否变更），有变更则标注变更点与影响方向；跨主体比较前确认财年截止月、上市地、币种三项是否一致，不一致的**逐项标注**，不做隐式对齐；一次性损益、并表范围变化、会计政策变更**必须单列**，不得混进趋势线。

---

## 阶段 5：缺口声明与 provenance（每稿必交）

**缺口是声明出来的，不是填出来的。** 三种形态分别处理：

| 缺口形态 | 表现 | 处理 |
|---|---|---|
| 工具不可用 | 未连接、报错、超时 | 明示"未连接该数据源（<工具名>）：<失败情形>"，该环节整体标注未核实 |
| 库内无覆盖 | 工具可用但目标标的/期数无记录 | 写"本地库无该标的的该期数记录"，给出可取的替代路径（如先落库） |
| 变量名不确定 | 检索不到目标变量 | 先用检索工具确认变量名；确认不了即视为**未取得** |

```
缺口清单：| 缺口项 | 需要的口径 | 尝试过的工具 | 返回状态 | 可取的路径 |
```

**provenance 表**（两列不得缺失，缺失一行则该行数值不得进入任何下游计算或叙述）：

| 输入项 | 数值 | 币种 | 单位 | 期间 | 来源（工具名 + 变量/指标名） | 数据期数 |
|---|---|---|---|---|---|---|
| <如 营业收入> | | CNY | 百万元 | 2025 财年 | `mcp__fd-find-data-business-mcp__gta_read` | 2025 |

报告末尾须汇总一行**数据截至**，把各通道时点分开写：

```
数据截至：财务与披露 <期别>（通道 A）｜市场类字段 <日期>（通道 B）｜计算算子 <调用日期>（通道 Q）
```

---

## 降级纪律

**通道 A 降级（逐级回落）**：某取数工具不可用时，在对应环节顶部明示"⚠️ 未连接该数据源（<工具名>）：<失败情形>"，依赖它的数值**留空写"未取得"**。回落顺序：`mcp__fd-cn-report__get_financial_statements` 失败 → 回落 `mcp__fd-cn-report__get_financials`；再失败 → 回落 `mcp__fd-find-data-business-mcp__gta_read` 按 firm-year 变量补齐；全链路失败则该环节整体降级。降级输出**整体标注"未经在线核实的推理草稿"**，不得写"数据显示""经核算"，不得生成看似真实的数值或期别；通道 A 未连接时**不产出正式数据底座**，改为交付需要的变量/科目清单 + 取数路径 + 待接入后复核的说明。

**通道 B 降级（价格类字段）**：价格类工具不可用或返回空时，**相关字段一律留空**，并在依赖它们的章节（估值、敏感性、技术面）明示"价格通道未就绪，该章所需数据未取得"；**不得**用账面值（如每股净资产）、同业价格或记忆里的数字冒充价格类输入。不依赖价格的章节（财务分析、竞争格局、治理与股权、财务数据）**照常交付**。

**通道 Q 降级**：计算面不可用时，派生比率与质量算子整段标"未完成（计算面不可用）"，**不得由模型自行计算顶替**；原始取数记录与口径表照常交付。

---

## 防编造硬约束与硬失败项（贯穿全程，红线）

- ❌ **所有数值、币种单位、报表期别、变量/指标名必须来自工具返回**；无返回如实写"未取得"，**禁止编造数值，不得凭记忆填写任何财务数据、指标代码或期别**。
- ❌ **不得猜测变量名或概念码调用读取工具**；先用检索工具确认再读，确认不了即视为未取得。
- ❌ **不得把通道 A 的账面值当作价格类输入**，不得把通道 B 的价格反推成财务科目。
- ❌ **不得用相邻期数、母公司口径、行业均值、同业值或记忆值顶替缺失值**；不得用"约""大概""估计在……左右"掩盖无出处；不得把不相关的工具返回张冠李戴到另一个字段上。
- ❌ **不得省略「来源」与「数据期数」两列**，不得省略「数据截至」汇总行；单位换算须写出原值与换算说明（如"原值 12,345 万元 → 1.23 亿元"）。
- ❌ **不得在实体未锁定时继续取数**（同名主体会污染整份报告）；不得引用未注册的工具名。

**硬失败项**（出现任一项，该稿作废重做）：未做覆盖度自检即输出数据底座；价格类字段取自非通道 B 来源，或财务科目取自通道 B；数值缺三件套之一即进入正文；provenance 表缺「来源」或「数据期数」列；缺口清单为空但报告中存在"约""大概"类表述。

---

> **免责声明**：本技能由 AI 辅助生成，输出为数据整理与口径说明，**不构成投资建议**，不能替代使用者的专业判断与独立复核。财务数据来自所标注的柏讯服务端通道 A，价格与市值类字段来自本地 DAAS 通道 B，计算类结果来自 fd-quant 计算面通道 Q，三类来源在输出中分别随附来源工具与数据期数。任何数据源未连接时，本技能**只交付框架与缺口清单，不产出正式数据底座**，相关输出标注为未经在线核实的推理草稿。结论对外使用前须经使用者按其自身口径复核全部数值与期别。
