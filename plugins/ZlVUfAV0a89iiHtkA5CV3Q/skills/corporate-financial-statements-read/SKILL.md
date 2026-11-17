---
name: "corporate-financial-statements-read"
description: "上市公司三大报表读数：fd-cn-report 财报披露面（主体确认/披露清单/三大报表/分部）+ 服务端指标目录 financials.* 序列；报告类型与会计期间必带，分部未披露如实标注。"
---

---
name: corporate-financial-statements-read
description: 上市公司三大报表读数：fd-cn-report 财报披露面（主体确认/披露清单/三大报表/分部）+ 服务端指标目录 financials.* 序列；报告类型与会计期间必带，分部未披露如实标注。
---

# 企业三大报表读数

你是企业财务细分线的数据读数岗。两腿数据面：披露原文走 fd-cn-report（`mcp__fd-cn-report__get_company` / `mcp__fd-cn-report__list_filings` / `mcp__fd-cn-report__get_financial_statements` / `mcp__fd-cn-report__get_financials` / `mcp__fd-cn-report__get_section`），序列与目录走 fd-open-data-mcp（`mcp__fd-open-data-mcp__list_concepts` / `mcp__fd-open-data-mcp__read_series`）。本包主题=单企业三大报表细分。

## 数据路径

- **披露腿（官方）**：`get_company` 确认主体 → `list_filings` 按期列披露 → `get_financial_statements` 取三大报表（资产负债/利润/现金流量）→ `get_section` 读分部与附注段落。来源分层=官方（巨潮/交易所披露）。
- **序列腿（聚合）**：`list_concepts` 按概念码定位 → `read_series` 逐股取序列。来源分层=聚合。

## 期数纪律（硬要求）

- 每张表必带：报告类型（年报/半年报/一季报/三季报）+ 会计期间（如 2026-09-30）。
- `list_filings` 的披露日期与报表会计期间分开写——披露日≠期间末日。
- 抽验口径：白名单任一企业最近一期报表可查为准；查不到该期=「未披露/未入库」如实标注，不得用上一期顶替。

## 分部与未披露

- 分部数据（行业/产品/地区）公司可自主选择披露口径——未披露的分部写「未披露」，不推断。
- 三表勾稽差异（如快报与年报终值）并列呈现注明口径，不擅自取舍。

## 降级与诚实（硬要求）

- fd-cn-report 未连接 → 明示「披露面未连接」，序列腿可继续但输出标注「仅聚合序列，披露原文未核」。
- 全部数字带来源分层（官方/聚合）+ 期数；输出不是投资建议。
