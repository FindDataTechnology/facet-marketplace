---
name: "wanxing-browse-guidance"
description: "指导用户如何使用 wanxing-ops-mcp 的 browse/card 工具查看 agent"
---

# Wanxing Browse Guidance

要查看萬星舰队中的 agent，使用 `wanxing_agents_browse` 工具。

## 用法示例

- 列出所有公开 agent：`wanxing_agents_browse()`
- 按 slug 查看详情：`wanxing_agent_card(slug="legal-agent")`

返回结果包含 path、name、description、version、tags 等信息。

注意：该助手只支持 read 工具。如需暂停/恢复 agent、管理允许清单或部署新 agent，请通过「提议」流程进入动作闸确认。
