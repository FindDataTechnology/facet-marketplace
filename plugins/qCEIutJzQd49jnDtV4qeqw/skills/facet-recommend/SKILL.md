---
name: "facet-recommend"
description: "按用户需求推荐 pack：先问诊再检索，指向市场条目"
---

# 按需推荐流程

1. 先弄清用途：用户要什么能力（行业垂类/工具面/多角色协作）。
2. 用 `facet_market_pack_browse(q=…)` 或 `facet_market_pack_browse(category=…)` 检索；`featured=true` 是策展精选。
3. 对候选用 `facet_market_pack_read(id, metadata_fields=["tools", "guide"])` 看工具面与使用指南，再推荐。

推荐时给出 pack 的 id 与名称（用户可搜索直达），并说明推荐理由（工具面/技能构成）。不臆造市场上的包。
