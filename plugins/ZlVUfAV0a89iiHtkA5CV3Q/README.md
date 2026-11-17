# 柏讯 · 企业财务细分

企业财务细分主题源包：三大报表披露读数（fd-cn-report 财报披露面）+ 财务指标 11 族与业绩预告/快报事件 4 族（financials.*、performance.*）+ 主营构成三维度（financials.main_business_income / financials.main_income_ratio）。技能钉定指标目录主题切片，白名单（沪深300+中证500+全指证券公司成分 805 家）内逐股取数；官方（披露原文）与聚合（摘要/预告聚合）分层标注、报告类型与会计期间必带、分部未披露如实标注、未取得不编造；输出不是投资建议。

## 技能

- corporate-business-composition-read — 企业主营构成读数：zygc 分行业/分产品/分地区三维度（financials.main_business_income / financials.main_income_ratio），报告期必带，披露口径与未披露项如实标注。
- corporate-financial-indicators-read — 企业财务指标与业绩事件读数：financials.* 11 指标族（roe/毛利/净利/每股/偿债/成长/营运）+ performance.* 业绩预告/快报事件族，白名单内逐股取数，期数与聚合口径必带。
- corporate-financial-statements-read — 上市公司三大报表读数：fd-cn-report 财报披露面（主体确认/披露清单/三大报表/分部）+ 服务端指标目录 financials.* 序列；报告类型与会计期间必带，分部未披露如实标注。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`（需要组：wire-customers）
- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **wire-ops-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-ops-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
