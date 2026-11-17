# 工厂集群

工厂集群：Owner 自用的三岗运行编制——巡检岗按可计数阈值观测全部在册集群与基础设施并逐条目落异常清单；优化岗把已确认异常只经既有提案面转为配置/蓝图/技能/跨面提案并附条目依据；报班岗按周汇总异常台账、提案四态计数与成本走势，拍板件走 decision_queue 正式呈报。提案与批准分离：红线动作永远人工，批准前零写动作。 含集群模板 fleet 段（三岗编排：巡检→优化→报班；装包后经壹座「团队→从模板实例化」建队）。

## 技能

- sentinel-discipline — 工厂集群巡检岗纪律：五类可计数阈值判据、逐条目异常清单格式、单回合口径收窄与收尾报数落 board。装配于工厂集群巡检岗。
- optimizer-discipline — 工厂集群优化岗纪律：异常→提案四通道映射、提案必附异常条目依据、幻觉源名防御、自动化政策线分级标注、批准前零写边界。装配于工厂集群优化岗。
- shiftlead-discipline — 工厂集群报班岗纪律：周报 digest 三段（异常台账/提案四态计数/成本走势）、拍板件走 decision_queue 正式呈报、禁私信替代。装配于工厂集群报班岗。

## 智能体

- 巡检岗
- 优化岗
- 报班岗

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **infra-telemetry** — 端点 `https://mcp.finddatatech.cloud/infra-telemetry/mcp`
- **fleet-architect** — 端点 `https://mcp.finddatatech.cloud/fleet-architect/mcp`
- **blueprint-author** — 端点 `https://mcp.finddatatech.cloud/blueprint-author/mcp`
- **facet-market** — 端点 `https://mcp.finddatatech.cloud/facet-market/mcp`
- **actions-mcp** — 端点 `https://mcp.finddatatech.cloud/actions-mcp/mcp`

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```

---

装好只是开始。想让这套能力 **7×24 常驻**（定时运行、多岗协作、产出直接交付），到萬星开一支智能体团队：<https://finddatatech.cloud>
