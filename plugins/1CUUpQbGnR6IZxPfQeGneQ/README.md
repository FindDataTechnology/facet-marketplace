# ERP 团队铸造舰

ERP 团队铸造舰的岗位 preset 源：巡检岗（只读盘矩阵格/在队团队/市场漂移，市场走公开只读端点）、设计岗（按缺口出团队草案并跑包级干跑）、拍板岗（如实报告上架依赖与阻塞）。工具面只引用 cell 内无需网关凭据的 fleet-architect 五件，逐岗经 resources.mcpTools 收窄；带 credentialRef 的 server 在无凭据时会被整层丢弃，故不引用。

## 智能体

- 巡检岗 — 只读盘点矩阵格承担者、市场未上架包与源市场漂移
- 设计岗 — 按缺口出团队草案：编制、节奏、KPI 槽位、逐岗工具面、看板声明，并跑包级干跑校验
- 拍板岗 — 唯一上架口：核定收口版本后把上架做成提案，批准才走上架通道

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **actions** — 端点 `https://mcp.finddatatech.cloud/actions/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
