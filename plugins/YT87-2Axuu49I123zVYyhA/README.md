# 柏讯本体建设团队

柏讯数据本体建设团队：对靶客户数据面持续盘账、建模、校验、经域提案箱提议落库，让实体关系图谱有真实内容。五岗流水线：盘账员（只读画像：行列规模、候选主键、疑似外键）→建模师（实体/关系声明草案，业务名从列名直译禁臆造）→纪律校验闸（确定性规则放行或拦截，给列名级原因）→声明提案员（经 bench_proposal_submit 提交到知验台本域提案箱（平台本体权威域），写操作永不直调）→台账主编（三点实调抽样对账，缺位如实标注）。全链只读+提案制：本体声明经域内专家在提案箱确认后进入平台本体，数据行零写回，实体与关系只建不改不删。

## 技能

- ontology-readonly-profiling — 只读取证流程：数据集清单→表结构→逐列四证据（唯一性/空值率/值域样本/外键候选）。
- ontology-draft-deterministic-rules — 草案自洽五判据：主键⊆列快照、关系目标∈主键集合、四元组唯一、1:n 与 n:1 互斥、中间集值域。
- ontology-action-proposal-protocol — 声明提案协议：一件一单、依据必填、词表纪律、幂等语义、拒绝不重试、可重试分开。

## 智能体

- 本体盘账员
- 本体建模师
- 纪律校验闸
- 声明提案员
- 台账主编

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`
- **bench-proposal** — 端点 `https://mcp.finddatatech.cloud/bench-proposal/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
