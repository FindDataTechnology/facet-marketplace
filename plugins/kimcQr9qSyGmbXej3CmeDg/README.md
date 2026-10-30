# 柏讯 · 因子选股

柏讯因子选股四件套：因子库与口径（52 因子九类清单、逐因子分子分母与报表期别、三个共线因子的剔除说明、数据可得性分档）、因子暴露与分层（走计算面，层数与极端值处理显式，IC 含 Pearson/Spearman，样本不足不作依据）、选股应用（多因子打分或条件筛选，产出候选 universe/目标权重，打分非预测）、无未来函数回测协议（财报用披露日而非报告期、universe 用当期成分、价格止于决策日、断言必须开启）。与自适应轮动套件的分工：轮动用价格类行业动量选组、本套件用基本面因子选股，两者可组合不重复；与个股投研套件的分工：那边做单只公司的深度报告，本套件做横截面的排序与筛选。因子值一律由计算面算出，不手算。回测为历史模拟，不构成投资建议。

## 技能

- factor-library — 柏讯因子选股 · 因子库与口径。当用户问"有哪些基本面因子可用""这个因子怎么算""为什么这两个因子不能同时用""某个因子在 A 股跑不跑得动"时使用。收录 FinRL-X 的 52 个基本面因子（九类），逐因子给出分子、分母、所用报表、期别、取值来源、数据可得性档位与 A 股适配说明，并写明三个共线因子的剔除理由。本技能只定义因子、不计算因子值、不选股、不做因子择时。
- factor-exposure — 柏讯因子选股 · 因子暴露与分层。当用户要求"算某只/某组标的的因子暴露""按因子分层看收益差""给我这个因子的 IC""这个因子在横截面上有没有区分度"时使用。暴露、分层、IC 与秩变换全部经 fd-quant 计算面完成，不在对话里手算；层数、极端值处理、样本门槛三处必须显式声明。产出：暴露表 + 分层收益表 + IC 数值与样本量。不做选股、不做因子择时。
- factor-screening — 柏讯因子选股 · 选股应用。当用户要求"按这些因子筛一批股票""多因子打分选前 N 只""把因子结论变成可回测的持仓"时使用。把 factor-exposure 的暴露与 IC 结果变成候选 universe 与目标权重，权重或规则显式写出，筛选条件逐条可解释，缺失因子按声明规则处理。产出可直接进入权重模式回测的候选名单/权重。因子打分不是预测。
- factor-backtest-protocol — 柏讯因子选股 · 无未来函数回测协议。当用户要把因子策略送进回测、要核对"这次回测有没有偷看未来"、要区分样本内与样本外、或要验某条未来数据会不会被拦住时使用。把四条点内纪律写成可核验的断言（财报按披露日、universe 用当期成分、价格止于决策日、断言必须开启），并用注入未来披露的夹具证明协议真的在拦。产出：回测口径卡 + 断言记录 + 夹具测试结果 + 样本内外分离的结论。不做因子择时、不给预期收益。

## 智能体

- 柏讯 · 因子选股助手

## MCP 连接指引

本插件声明以下 MCP 引用（**不随插件自动连接**——插件本体不携带任何服务器配置或凭据）：

- **fd-quant** — 端点 `https://mcp.finddatatech.cloud/fd-quant/mcp`（需要组：wire-customers）
- **fd-cn-report** — 端点 `https://mcp.finddatatech.cloud/fd-cn-report/mcp`（需要组：wire-customers）
- **fd-find-data-business-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-find-data-business-mcp/mcp`（需要组：wire-customers）
- **fd-paper-trading** — 端点 `https://mcp.finddatatech.cloud/fd-paper-trading/mcp`（需要组：wire-customers）
- **fd-daas-mcp** — 端点 `https://mcp.finddatatech.cloud/fd-daas-mcp/mcp`（需要组：wire-customers）
- **wire-build-mcp** — 端点 `https://mcp.finddatatech.cloud/wire-build-mcp/mcp`（需要组：wire-customers）

连接方式：支持 MCP OAuth 的客户端可直接对该端点授权；否则用谦面 CLI 铸取调用键后写入本机配置：

```
npx @finddatatechnology/facet connect
npx @finddatatechnology/facet install <packRef> --target workbuddy --write-mcp
```
