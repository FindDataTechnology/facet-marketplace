# 柏讯 · 产业链联动

柏讯产业链联动四件套：链条映射（上中下游环节可多级、代表标的与归属依据、每环关键变量与来源、两档映射置信度）、传导验证（逐环相关性 + 时滞扫描 + 样本量与窗口，未验证环节明示不默认成立，不显著如实呈现，频度差处理，非因果表述）、环节受益分析（基于已验证的传导结论给候选，逐候选项回指传导依据，未验证环节不作依据，不预测价格）、链条景气跟踪（按环节分组的变量走势 + 来源与最新期数 + 缺口标注，无预测无建议）。与既有「行业链分析」技能的关系：那是分析技能，本套件是它的可安装套件化 + 可执行出口版本（从产业链关系走到可回测的权重），不是重复实现。核心纪律：产业链叙事最容易滑向讲漂亮故事——上游涨价到中游承压再下游提价听起来总是对的，但时滞、幅度与是否发生都必须有数据支撑；无数据的那一环明示未验证。传导结论是历史统计关系不是因果证明，不构成投资建议。

## 技能

- chain-mapping — 柏讯产业链联动 · 链条映射。当用户要求"把这个行业拆成上下游""这几家公司分别在产业链哪一环""这条链的结构和关键变量是什么"时使用。产出：环节结构（上/中/下游，可多级）+ 代表标的（逐家给可解释的归属依据）+ 每环关键变量与来源 + 两档映射置信度（分类体系支持 / 公开资料推断）。只做结构映射，不做传导检验（交给 chain-transmission），不出候选（交给 chain-beneficiary）。
- chain-transmission — 柏讯产业链联动 · 传导验证。当用户说"上游涨价能不能传到中游毛利""这条链的滞后有多长""这个传导到底成不成立"时使用。产出：逐环传导检验表——每环给相关系数、时滞扫描、样本量与窗口；无数据的环节明示"未验证"，不显著的结果如实呈现，频度差显式处理。结论是历史统计关系，不是因果证明。
- chain-beneficiary — 柏讯产业链联动 · 环节受益分析。当用户说"这条链里哪个环节该配""毛利承压环节选哪几只""把传导结论变成可回测的候选"时使用。产出：可执行候选 universe 与目标权重（可进权重模式回测），每个候选回指某条已验证的传导结论；未验证环节不作依据。不预测价格、不给涨跌判断。
- chain-watch — 柏讯产业链联动 · 链条景气跟踪。当用户要求"盯一下这条链最近怎么样""按环节给我看关键变量的最新走势""哪些数据好久没更新了"时使用。产出：按环节分组的变量走势表（来源 + 最新期数 + 缺口标注 + 变化方向），不含预测、不含建议。宿主 cron 可定时触发（本技能只给方法与输出形态，不实现调度）。

## 智能体

- 柏讯 · 产业链联动助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
