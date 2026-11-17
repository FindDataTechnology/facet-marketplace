# 识律合同签署团队

lex 合同签署团队模板（stub 内测口径）：发起与跟催岗经柏讯动作闸只做发起/催签/查状态/归档四动作，pending 后本轮收工下轮轮询；签署只发生在唯一人类确认面，岗位结构性无代签能力。装包后经壹座「团队→从模板实例化」建队；电子签四纪律随 persona 与 esign-gate-discipline 技能自带（红线级不委托共享包），无共用层依赖。stub 内测通道无司法效力宣称。

## 技能

- esign-gate-discipline — 电子签动作闸纪律卡：四动作边界、适用性闸门语义、stub 内测口径与禁语清单。

## 智能体

- 发起与跟催岗 — 电子签任务发起、催签与状态巡检（经动作闸，无直发权）

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wire-ops** — 端点 `https://mcp.finddatatech.cloud/wire-ops/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
