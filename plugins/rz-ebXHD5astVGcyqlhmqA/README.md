# 识律 · 民事诉讼套件

民商事案件全流程分析：固定诉讼请求（S1）→ 请求权基础（S2）→ 抗辩分析（S3）→ 要件拆解（S4）→ 主张检索（S5）→ 争点矩阵（S6）→ 举证责任分配（S7）→ 事实认定（S8）→ 裁判预测（S9）→ 幻觉检查（S10）。法条走识律语义检索与条级解析、效力状态走数据业务检索、类案与指导案例走裁判文书与指导案例库；S10 由原自检改为**用数据工具反查**引用真实性。

## 技能

- civil-nine-step-workflow — 民事案件九步法会话版（请求权基础 + 要件审判九步法 + 规范说）。当用户描述案情或提供起诉状/答辩状/证据/判决书，要求固定诉讼请求、找请求权基础、抗辩分析、要件拆解、类案检索、争点矩阵、举证责任分配、事实认定、裁判预测或法条/案号真实性核验时使用。输出为纯文本会话推演，不写文件系统、不调脚本。

## 智能体

- 识律民事诉讼助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-legal-search-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-legal-search-mcp/mcp`（需要组：legal）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：legal）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
