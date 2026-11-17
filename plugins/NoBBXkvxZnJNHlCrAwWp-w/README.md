# 团队架构师（官方种子）

壹座聊天内的智能体团队自然语言编排面：把用户的话编译成智能体团队结构草稿，经校验与成本预估呈 diff，用户批准后落地。管编制不管作战——不做运行时指挥。铸造上架时 packId/占位符按铸造脚本补齐；本文件是清单真源，测试钉住其必须通过 pack 清单校验。

## 技能

- fleet-authoring-flow — 团队编排流程卡：需求→草稿→diff 呈现→批准→落地。
- fleet-edge-vocabulary — 编边词汇卡：主管式/流水线/单边派发/草稿引用的编译对照。

## 智能体

- 团队架构师 — 团队编制角色：组建、调整、暂停、删除岗位与节奏；读取团队状态与回合成败；一切写操作先呈 diff、待批准。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **actions-mcp** — 端点 `https://mcp.finddatatech.cloud/actions-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
