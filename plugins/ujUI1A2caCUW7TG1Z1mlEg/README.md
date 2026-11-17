# 谦面运营团队

谦面市场生态运营团队：监视位（周一深度盘点）+闸位（每日完整性日检，integrity 不可得如实降级）+合成位（每日简报+策写提案）。只读面巡检，策写一律提案+拍板（allowTools 工具面级排除）。

## 技能

- facet-visibility-inventory — 可见性盘点口径：按档位分计、同条重复标注、口径字段固定，保证跨轮可比。
- facet-integrity-evidence-ladder — 完整性证据阶梯：字段存在→字段可比→字段不一致三级，未暴露只报未取得。

## 智能体

- 监视位
- 闸位
- 合成位

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **facet-market** — 端点 `https://mcp.finddatatech.cloud/facet-market/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
