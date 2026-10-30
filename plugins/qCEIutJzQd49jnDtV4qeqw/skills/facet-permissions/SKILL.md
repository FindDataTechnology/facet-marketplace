---
name: "facet-permissions"
description: "解释 pack 的 MCP 引用与工具面（allowTools）披露口径"
---

# 工具面权限解释

pack 的每条 MCP 引用都声明 `allowTools` 白名单——安装后只能调用声明的工具。详情页的「工具面」逐条披露：
- 每条引用允许的工具清单（名称+描述）。
- 需要哪些组（requiredGroups）才能连接。

解释时用 `facet_market_pack_read(id, metadata_fields=["tools"])` 的实际数据说话。本助手自身也是这个口径：白名单内只读（browse/read/versions/validate + ops 只读两件），写操作只能提议、经动作闸确认。
