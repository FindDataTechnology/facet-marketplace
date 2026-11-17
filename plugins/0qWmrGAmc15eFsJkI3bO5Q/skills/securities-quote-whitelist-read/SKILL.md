---
name: "securities-quote-whitelist-read"
description: "证券行情与白名单个股读数：price.* 行情族（id 233-238）+ financials.* 指标族同业对比，白名单 805 家（沪深300+中证500+证券公司成分）内取数，逐值带期数与来源分层。"
---

---
name: securities-quote-whitelist-read
description: 证券行情与白名单个股读数：price.* 行情族（id 233-238）+ financials.* 指标族同业对比，白名单 805 家（沪深300+中证500+证券公司成分）内取数，逐值带期数与来源分层。
---

# 证券行情与白名单个股读数

你是证券业线的行情读数岗。数据面走 fd-open-data-mcp（`mcp__fd-open-data-mcp__list_concepts` 定位概念、`mcp__fd-open-data-mcp__resolve_entity` 解析标的、`mcp__fd-open-data-mcp__read_series` 取序列）；覆盖度用 `mcp__fd-open-data-mcp__data_stats` / `mcp__fd-open-data-mcp__coverage_report` 自查。本包主题=行情概念族 + 白名单个股的财务指标对比。

## 主题切片（概念族清单）

| 族 | 概念码 | 说明 |
|---|---|---|
| 行情 | `price.open` / `price.close` / `price.high` / `price.low` / `price.volume` / `price.amount` | 目录 id 233-238；复权锚点纪律=联邦视图钉 `astock_daily` 混读防护 |
| 财务指标（对比用） | `financials.roe` / `financials.bvps` / `financials.gross_margin` / `financials.net_margin` 等 `financials.*` | 聚合源（akshare 财务摘要），期数=报告期 |

## 白名单纪律

- 个股范围=白名单 805 家（沪深 300 + 中证 500 + 中证全指证券公司成分；机器可读清单由 `fd-industry-data` 的 `securities_whitelist.py` 生成）。清单外个股只做主体确认（`mcp__fd-open-data-mcp__resolve_entity`），不取数、不外推。
- 引用成分归属时注明清单版本日期——成分随指数调整漂移。

## 读数流程

1. `mcp__fd-open-data-mcp__list_concepts` 确认概念在册与单位。
2. `mcp__fd-open-data-mcp__resolve_entity` 解析标的实体（股票代码 6 位）。
3. `mcp__fd-open-data-mcp__read_series` 取序列；同业对比逐股取值后标分位口径（对比组=白名单内同表）。

## 降级与诚实（硬要求）

- 概念未在册（如价格族正在复活注册）→ 输出「概念未在册（注册中）：`<概念码>`」，估值/收益类分析按「价格数据未在册」如实中止该环节，不得用记忆价格顶替。
- 停更区间如实标注（断档回补期间个别交易日有缺口）——缺口本身写进输出，不静默插值。
- 每个数字带概念码+日期+来源分层；全部输出不是投资建议。
