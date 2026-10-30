# 数据 · 指标工坊

谦面指标工坊创作五件套：规则驱动实体集、指标持久化、指标集策展、统一规则（json/script/position/llm）与单文件 HTML 看板——把业务口径沉淀为可复用的数据资产

## 技能

- fd-daas-entities-collection-creator — 创建规则驱动的实体集（自选股/组合）：声明式 JSON 规则，跨表/动态逻辑落 Python 规则脚本可重跑
- fd-daas-indicators-creator — 端到端建指标并持久化：扩展取数流程落 scraw_<slug> 存储表并把序列数据存进表（刷新为手动重跑）
- fd-daas-indicators-collection-creator — 策展指标集：把指标组成命名集合、按三级解析给出各成员 resolved score，导出机器可读 CSV + 人类可读简介
- fd-daas-rules-creator — 统一规则创作（json/script/position/llm 四型）：daas_create_rule 落库、daas_test_rule 试跑，可挂实体/指标集合并同步
- fd-daas-dashboard-creator — 把 daas 数据做成单文件独立 HTML 看板：ECharts + 实体/时间交互筛选，注册进 dashboards 表并迭代修改

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
