---
name: "facet-validate-guidance"
description: "指导用户如何使用 validate 进行发布前预检"
---

# Publish Pre-validation

在发布新包或新版本前，先用 `facet_market_pack_validate` 做 dry-run 检查。

## 用法示例

```json
{
  "manifest": {
    "name": "我的法律助手",
    "description": "...",
    "skills": [...],
    "mcpServers": [{ "registryName": "fd-open-data-mcp", "allowTools": [...] }]
  }
}
```

validate 会返回 `{valid: true/false, errors: [...]}`，错误清单会指明需要修正的问题（如 allowTools 格式、技能名合法性等）。

验证通过后才能调用 `facet_market_pack_publish`。
