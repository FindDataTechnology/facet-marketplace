# 柏讯 · 模拟交易套件

柏讯模拟交易（paper trading）三件套：对话操作（账户/持仓/下单/撤单）、策略工作流（模板与沙箱自定义代码、回测解读、纸面实盘）、行情检查（搜索/历史K线/覆盖边界诚实呈现）。paper-only：模拟盘、无真实资金；调用者身份逐请求鉴别，只操作自己的账户；行情覆盖由部署的概念族决定，技能以诚实呈现覆盖边界为第一纪律。

## 技能

- paper-trading-conversation — 柏讯模拟交易（paper trading）对话操作。当用户要求查看纸面账户与余额、开新模拟账户、查持仓明细（数量/成本/浮盈亏）、下单买入或卖出（市价/限价）、撤单、查订单历史，或询问某标的当前价格时使用。走柏讯模拟交易服务（paper-only 模拟盘，无真实资金、无真实订单），调用者身份逐请求鉴别——只操作自己的账户。
- paper-trading-strategy — 柏讯模拟交易策略工作流。当用户要求创建量化策略（SMA 交叉/RSI 反转/布林突破/MACD 交叉/定投等模板，或自定义 on_bar 代码）、跑历史回测并解读权益曲线与统计（收益/回撤/夏普/胜率）、在模拟账户上启动或停止纸面实盘运行、查看运行历史时使用。paper-only：策略只在模拟盘上下单。
- paper-trading-market-inspection — 柏讯模拟交易行情检查。当用户要求搜索某个可交易标的（按代码或名称）、查看某标的的历史 K 线（OHLCV）、了解当前部署行情面覆盖哪些标的类型、或为下单/回测确认某标的数据是否可用时使用。行情来自柏讯模拟交易服务的行情面（概念族可配，默认基金净值），覆盖面有限——本稿核心职责是诚实呈现覆盖范围。

## 智能体

- 模拟交易助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
