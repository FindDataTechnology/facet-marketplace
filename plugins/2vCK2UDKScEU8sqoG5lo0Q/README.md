# 餐饮行业监测团队

餐饮行业监测团队：监视位读宏观、成本链、公司财务与披露事件并给出缺口归因，合成位交叉复核产出值守记录，产出位落草稿产物。只读纪律，不写观测、不发布对外产物。

## 技能

- restaurant-watch-discipline — 餐饮监测岗纪律：缺失三分类归因、不插值不补 0、不可得清单、趋势门槛与只读边界。

## 智能体

- 餐饮监视位 — 读取餐饮垂直线宏观、成本链、公司财务与披露事件，产出带缺口归因的读数清单。
- 餐饮合成位 — 交叉复核读数，裁决缺口归因，产出含告警/异常/待采/不可得四节的值守记录。
- 餐饮产出位 — 把定稿的值守记录落成草稿产物，只创建草稿不发布。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-open-data-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-open-data-mcp/mcp`
- **fd-info** — 端点 `https://mcp.finddatatech.cloud/fd-info/mcp`
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
