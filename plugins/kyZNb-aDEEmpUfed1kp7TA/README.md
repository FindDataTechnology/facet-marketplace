# 启材盯梢

启材线（教育）盯梢舰队模板：盯梢哨每回合对教育目录出逐条目增量清单（新资源/更新/撤回，类别/目标/观测值三栏机器可数），零增量照记台账，通道故障显式报因；写面=订阅两工具（唯一写面），Owner 授意的订阅变更走 escalate。单回合口径收窄防超时。

## 技能

- edu-watch-sentinel — 启材盯梢哨纪律：三步收窄工作流（概况盘点/订阅增量/撤回巡检），清单三栏机器可数，零增量照记。

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **edu-catalog** — 端点 `https://mcp.finddatatech.cloud/edu-catalog/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
