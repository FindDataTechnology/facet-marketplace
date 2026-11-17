# 酒店行业监测团队

酒店行业监测团队：监视位读星级饭店季度口径、出游与出入境需求、欧盟住宿序列与 33 家酒店公司财务细分并给出缺口归因；合成位交叉复核产出值守记录；产出位落草稿产物。只读纪律，不写观测、不发布对外产物。

## 技能

- hotel-watch-discipline — 酒店监测岗纪律：四套宇宙不可混算、缺失三分类归因、不插值不补 0、趋势门槛与只读边界。

## 智能体

- 酒店监视位 — 读取酒店垂直线星级饭店、出游需求、欧盟住宿与公司财务细分观测，产出带缺口归因的读数清单。
- 值守记录合成位 — 交叉复核监视位读数与缺口，产出当班值守记录：口径一致、缺口归因、异常判定。
- 草稿产物位 — 把值守记录落成草稿产物（看板说明/日报草稿），不发布、不对外。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`
- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`
- **fleet** — 端点 `https://mcp.finddatatech.cloud/fleet/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
