# 餐饮连锁对账连接器包

餐饮连锁对账模板·连接器 pack：POS BYOK 适配器框架（框架一个、厂商逐个插拔；首个适配厂商由首单客户的实际厂商决定，不预设名单；未适配厂商如实报出 + 导出文件替代通道）+ 收款渠道商户侧适配（微信支付/支付宝/美团商户侧 API 结算流水拉取，可用面以实测为准，未实测不宣称 live）+ 外卖结算读取暂无目录覆盖（美团在目录里只有酒旅查询，故不声明）。凭证逐店授权 BYOK 环境变量注入零落库。只读滩头：对渠道/平台侧零写入；无资金执行面（二清红线）。

## 技能

- erp-restaurant-recon-discipline — 餐饮连锁对账纪律卡：门店三方对账、日结三段、未适配厂商如实报出、导出文件替代通道、渠道侧零写入、资金红线。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-erp-ledger-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-erp-ledger-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
