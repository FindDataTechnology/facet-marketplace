---
name: "facet-gateway-ops"
description: "谦面网关（registry）操作手册：AgentCard 目录浏览/登记/更新/启停/查重的安全用法与错误语义。"
---

# 谦面网关操作（facet-gateway-ops）

## 工具面（facet_agent_* / facet_catalog_browse）

- facet_catalog_browse：网关目录（query/limit/offset 分页）
- facet_agent_read：条目详情（404=无此条目）
- facet_agent_publish：登记新 AgentCard——必填 name / url / supportedProtocol；is_proxied=true 需配 proxy_target_url
- facet_agent_update：更新已有定义（仅属主/有权者）
- facet_agent_toggle：启用/停用（enabled 显式布尔）
- facet_agent_duplicate_check：登记前查重（恒 200，advisory 不阻塞）

## 身份与错误语义

每个调用以你的 registry 凭据执行（壹座注入）；可见域与写权限由网关判定。401=凭据失效（提示用户重连）；403=无权限（如实转述，别绕）；404=无此条目；unreachable=网关故障（不冒充业务错误）。空结果如实为空，绝不编造。

## 纪律

- 登记/更新前必查重；写操作先 facet_agent_read 确认属主与现状。
- 401 后停止写操作，提示用户到设置重连 registry 凭据。
- 面边界：本面包的是网关 AgentCard 面；功集发布走 facet_market_*（见姊妹技能）。
