# 谦面助手

在壹座中操作谦面的专用助手——浏览包目录、读详情、发布前校验、建包向导。只读工具 + 提议通道，所有写操作需经动作闸确认。

## 技能

- facet-browse-guidance — 指导用户如何使用 facet-market-mcp 的 browse/read 工具搜索包
- facet-validate-guidance — 指导用户如何使用 validate 进行发布前预检
- facet-concepts — 回答 facet 概念问题：pack 是什么、订阅与安装是两件事、版本快照语义
- facet-recommend — 按用户需求推荐 pack：先问诊再检索，指向市场条目
- facet-permissions — 解释 pack 的 MCP 引用与工具面（allowTools）披露口径
- facet-install-guidance — 两条安装路径引导：壹座一键订阅安装 / CLI 安装到 Claude Code / Cursor

## 智能体

- 谦面助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **facet-market-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-market-mcp/mcp`
- **facet-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/facet-ops-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
