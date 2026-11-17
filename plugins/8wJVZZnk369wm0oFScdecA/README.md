# 萬星机群运营团队

萬星团队运行面运营团队：监视位（fleets.db 只读直查，连续失败点名到 member）+归账位（wanxing 只读盘点，凭据缺位如实降级）+合成位（每日简报+治理提案）。治理写操作一律提案+拍板（allowTools 工具面级排除）。

## 技能

- fleet-readonly-db-patrol — fleets.db 只读巡检配方：表清单、成员态枚举、连续失败计数口径与 vacant 时长算法。
- fleet-governance-proposal-card — 治理提案卡：现象/证据/选项/影响面四栏，写面动作一律提案不自决。

## 智能体

- 监视位
- 归账位
- 合成位

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **wanxing-ops** — 端点 `https://mcp.finddatatech.cloud/wanxing-ops/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
