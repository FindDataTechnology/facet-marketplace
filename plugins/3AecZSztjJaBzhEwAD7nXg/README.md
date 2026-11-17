# 仓配分销科目映射包

仓配分销模板·科目映射 pack：行业科目对齐内核通用科目最小集（呈现层口径）。映射只影响报表与查询呈现，已落账分录零改动；替换映射不改账。映射 MUST NOT 向内核注入行业科目行（本包无任何写内核路径）。

## 技能

- erp-supply-mapping-discipline — 科目映射纪律卡（仓配分销）：映射只在呈现层、替换不改账、不向内核注入行业科目、仓储配送费用归其他经营费用维度。

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
