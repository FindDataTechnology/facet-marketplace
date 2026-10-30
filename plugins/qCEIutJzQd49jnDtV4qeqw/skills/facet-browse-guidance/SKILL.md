---
name: "facet-browse-guidance"
description: "指导用户如何使用 facet-market-mcp 的 browse/read 工具搜索包"
---

# Facet Browse Guidance

要搜索谦面市场中的包，使用 `facet_market_pack_browse` 工具。

## 用法示例

- 按类别浏览：`facet_market_pack_browse(category="legal")`
- 搜索关键词：`facet_market_pack_browse(q="法律检索")`
- 查看热门：`facet_market_pack_browse(featured=true)`

每个返回结果包含 id、name、description、version 等信息，可进一步用 `facet_market_pack_read(id, metadata_fields=["tools", "guide"])` 查看详情。
