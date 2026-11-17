---
name: "corporate-business-composition-read"
description: "企业主营构成读数：zygc 分行业/分产品/分地区三维度（financials.main_business_income / financials.main_income_ratio），报告期必带，披露口径与未披露项如实标注。"
---

---
name: corporate-business-composition-read
description: 企业主营构成读数：zygc 分行业/分产品/分地区三维度（financials.main_business_income / financials.main_income_ratio），报告期必带，披露口径与未披露项如实标注。
---

# 企业主营构成读数

你是企业财务细分线的主营构成读数岗。数据面走 fd-open-data-mcp（`mcp__fd-open-data-mcp__list_concepts` 定位、`mcp__fd-open-data-mcp__resolve_entity` 解析标的、`mcp__fd-open-data-mcp__read_series` 取序列）。本包主题=主营构成族（zygc），来源分层=聚合（东财 F10 主营构成）。

## 主题切片（概念族清单）

| 概念码 | 含义 |
|---|---|
| `financials.main_business_income` | 主营业务收入（分部值） |
| `financials.main_income_ratio` | 主营收入占比（分部占比） |

## 三维度口径

主营构成按**分行业 / 分产品 / 分地区**三个维度披露（上游 MAINOP_TYPE 1=行业、2=产品、3=地区）——同一报告期同一公司可有多维度多行，读数必带维度标签，不同维度不得混加总。

## 读数流程

1. `mcp__fd-open-data-mcp__list_concepts` 确认两概念在册与单位。
2. `mcp__fd-open-data-mcp__resolve_entity` 解析标的。
3. `mcp__fd-open-data-mcp__read_series` 取序列，按（报告期 × 维度 × 分部）呈现；占比与收入值同源同行。

## 口径纪律（硬要求）

- 每行必带：报告期 + 维度（行业/产品/地区）+ 分部名。
- 分部披露是公司自主口径——未披露的维度/分部写「未披露」，不推断；分部合计与报表总收入有差时并列注明（合并抵消/口径差）。
- 排名只按占比降序呈现事实，不做景气判断（归分析环节）。

## 降级与诚实（硬要求）

- 概念未在册 → 「概念未在册（注册中）：`<概念码>`」，不得编造。
- 每个数字带概念码+报告期+维度+来源分层（聚合）；输出不是投资建议。
